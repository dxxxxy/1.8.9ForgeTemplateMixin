# 1.8.9ForgeTemplateMixin
> Mixin version of [DxxxxY/1.8.9ForgeTemplate](https://github.com/DxxxxY/1.8.9ForgeTemplate)
> 
A fixed and ready to use template for minecraft forge modding on `1.8.9`
with `mixin` and `shadow` plugins integrated including two useful `artifacts`. Tested flawlessly on `IntelliJ IDEA Ultimate 2022.2`

```js
        java   8
      gradle   3.1
      shadow   2.0.4
   minecraft   1.8.9
 forgegradle   2.1-SNAPSHOT
 mixingradle   0.6-SNAPSHOT
mixinlibrary   0.7.10-SNAPSHOT
```

## Setup
1. Clone this repository.
2. Import `build.gradle` as a project.
3. Let it configure.
4. Run `setupDecompWorkspace` in Gradle: `Tasks > forgegradle > setupDecompWorkspace`
5. Reload gradle project.
> *Check out [#build](#build), [#run](#run), [#mixin](#mixin) and [#artifacts](#artifacts) for more information.*

## Build
> *Note: If the build ever finishes with an error looking like `duplicate entry`, then run `clean` in Gradle: `Tasks > build > clean`*

&emsp;Run `build` in Gradle: `Tasks > build > build`
> Output .jar will be located in `build/libs/`

## Run
&emsp;Run `genIntellijRuns` in Gradle: `Tasks > forgegradle > genIntellijRuns`
> This will create the run configurations. <br> *Note: you will need to change the classpath module to `.main`*

> You should add `CopyResources` from [#artifacts](#artifacts) as a `Task before launch` and drag it in front of build.

## Artifacts
> *Note: If the artifacts are not detected on setup, you might need to restart your IntelliJ.*

&emsp;I have included two useful artifacts that I always use when developing mods:
- `CopyMod` copies the mod from your [#build](#build) folder into your `%appdata%/.minecraft/mods`
- `CopyResources` copies the resources folder to your `build/classes/main` so they are accessible in the development environment


## Mixin
> This section will cover how to set up mixin the right way.

`.../build.gradle`:
```groovy
//...

jar {
    manifest {
        attributes "FMLCorePlugin": "studio.dreamys.mixin.MixinLoader", //change this to your path to MixinLoader class
                "ForceLoadAsMod": true,
                "TweakOrder": 0,
                "ModSide": "CLIENT",
                'FMLCorePluginContainsFMLMod': true,
                'TweakClass': 'org.spongepowered.asm.launch.MixinTweaker',
                'MixinConfigs': 'mixins.examplemod.json' //change examplemod to your modid
    }
}

sourceSets {
    main {
        ext.refMap = "mixins.examplemod.refmap.json" //change examplemod to your modid
    }
}

//...
```

`.../src/main/resources/mixins.examplemod.json`:
```json
{
  "required": true,
  "package": "studio.dreamys.mixin", //change this to your path to mixin package
  "refmap": "mixins.examplemod.refmap.json", //change examplemod to your modid
  "compatibilityLevel": "JAVA_8",
  "client": [
    "client.MixinMinecraft"
  ]
}
```

`../src/main/java/studio/dreamys/mixin/MixinLoader.java`:
```java
public class MixinLoader implements IFMLLoadingPlugin {
    public MixinLoader() {
        System.out.println("[ExampleMod] Injecting with IFMLLoadingPlugin.");
        MixinBootstrap.init();
        Mixins.addConfiguration("mixins.examplemod.json"); //change examplemod to your modid
        MixinEnvironment.getDefaultEnvironment().setSide(MixinEnvironment.Side.CLIENT);
    }
    
//...
```
### Optional
If you ever want to debug with mixins in your client, add this to your VM options in the `Minecraft Client` run configuration:

`-Dfml.coreMods.load="studio.dreamys.mixin.MixinLoader"`

*Change to your path to MixinLoader class.*

However, Hotswap will not work even if you don't modify the mixin.