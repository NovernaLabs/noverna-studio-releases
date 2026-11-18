# Getting started: BepInEx 5 mods with Noverna Studio

This guide takes you from an empty folder to a BepInEx 5 mod running in the game, using Lethal Company as the example. Any Unity game with Mono scripting works the same way.

## 1. Pick the game and install BepInEx

1. Start Noverna Studio. The launcher lists the games it found in your Steam libraries; add others with **Add Game…**.
2. Select the game. Noverna shows its Unity version, scripting runtime (Mono or IL2CPP) and whether an anti-cheat is present. Don't mod games with anti-cheat online.
3. Under **Mod framework**, choose **BepInEx 5** and **Install**. Noverna downloads the official release and installs it into the game folder. Start the game once so BepInEx can set itself up.

> IL2CPP games: use MelonLoader instead (see the MelonLoader guide). BepInEx 6 is supported for projects, but its IL2CPP live features are on hold.

## 2. Create the project

1. **New project** → choose the game, framework and a template (*Plugin*, *Plugin + Harmony* or *Plugin + Config*).
2. The project wizard checks your toolchain (.NET SDK, Git). Missing tools can be installed with one click (via winget, after you confirm).
3. Noverna creates a `.novproject` (the project file), a `.csproj` that references the game's assemblies, and `Plugin.cs`.

The `.novproject` leads: name, ID, version and dependencies live there, and Noverna keeps `.csproj`, `[BepInPlugin]` and friends in sync (tab **Mod Metadata**).

## 3. Build and run

- **Build** (`Ctrl+Shift+B`) writes to `dist\Debug` (or `dist\Release`), already laid out like the game folder. Nothing is copied into the game.
- **Run** (`F5`) starts a *session*: the mod, the dependencies of the profile and the Noverna agent go into the game for this run only and are removed afterwards, even after a crash.
- Profiles (title bar): **Clean** loads only BepInEx, your mod and its dependencies, in an isolated profile outside the game folder. **With my mods** adds what you installed yourself (compatibility test).
- The **Game Log** shows the BepInEx log live; errors land in **Problems** with a jump to your code.

## 4. Understand the game

- **Assembly browser**: open any game assembly from the project tree, search symbols with `Ctrl+T`, decompile, see IL, and use **Analyze** (*Used by*, *Calls*, *Assigned by*).
- **Create Harmony patch** on a method in the browser generates a prefix, postfix or transpiler with the right signature. Noverna checks your patches as you type (NVH001–NVH005).
- **Asset browser**: textures, audio, meshes, scenes and AssetBundles of the game, read-only. *Copy as code* gives you the path or loading code.
- **Insert snippet** (`Ctrl+Alt+J`): config entries, lifecycle methods, Harmony patches, Netcode RPCs and more, filtered for your game and framework.

## 5. Work live

While the game runs from Noverna:

- **Live** tab: the scene hierarchy, components and their values; change values directly. **Pick** an object in the game (`Ctrl+Alt+P`), or highlight one from the tree.
- **Watch** panel: pin values and see them change.
- **Console**: run C# against the game.
- **Hot reload** (`Ctrl+Alt+R`): rebuild and swap your mod without restarting. Implement `OnUnload()` to undo what `Awake` set up.
- **Debugger** (`F5`): breakpoints, stepping, locals and break on your exceptions (Mono games).
- **Config Editor**: your mod's `.cfg` as a form; *Apply to game* sets values while it runs.

## 6. Dependencies

Packages panel: add other mods' libraries from a GitHub release, NuGet or a local DLL. They are referenced, loaded in sessions, and checked before start (missing plugins, versions). Declare plugin dependencies with `[BepInDependency]`; the release check reminds you.

## 7. Your own assets

Tab **Mod Assets**: put your own textures, sounds and AssetBundles into `Assets\`. Every build copies them next to the mod (`ModAssets.PathOf("…")` finds them at runtime). For models and prefabs, create the **Unity companion project**: Noverna builds its AssetBundles before every build. To change a game texture, use **Replace in my mod** in the asset browser: you ship only your own image, never the game's file.

## 8. Test

- **Tests** tab: unit tests (xUnit, `Tests\` project) and runtime tests (`[RuntimeTest]`) that run inside the game.
- **Compatibility** tab: static checks of the mods a session would load, and **Narrow down a conflict**: Noverna starts the game with different groups of mods until it finds the smallest group that shows an error.
- After a game update, Noverna compares the game's code with the previous version (**Game Update** tab) and tells you which of your patch targets changed.

## 9. Release

1. Set the version in **Mod Metadata** (it updates every place it appears).
2. **Check release**: builds Release and checks `dist\Release` (no debug files, no copies of game or framework assemblies, all assets present, dependencies declared, no files exported from the game).
3. Ship the contents of `dist\Release`. (Publishing on NovernaLabs comes later.)
