# Getting started: MelonLoader mods with Noverna Studio

MelonLoader runs on Mono and IL2CPP Unity games. This guide uses Schedule I (IL2CPP) as the example; differences for Mono games are noted.

## 1. Pick the game and install MelonLoader

1. In the launcher, select the game. Noverna shows its Unity version and runtime (IL2CPP or Mono).
2. Under **Mod framework**, choose **MelonLoader** and **Install** (0.6 or newer). On IL2CPP games MelonLoader needs the .NET 6 runtime; Project Health offers to install it.
3. Start the game once. On IL2CPP games MelonLoader generates the *interop assemblies* (`MelonLoader\Il2CppAssemblies`) your mod compiles against. This takes a while the first time.

> If MelonLoader can't generate them, the editor offers **Generate assemblies** (Cpp2IL) so you can browse the game's code anyway.

## 2. Create the project

**New project** → game, **MelonLoader**, template (*Mod*, *Mod + Harmony* or *Plugin*). Noverna writes the `.novproject`, a `.csproj` against the right assemblies (.NET 6 interop on IL2CPP, the game's `Managed` folder on Mono) and `Mod.cs` with `[assembly: MelonInfo]` and `[assembly: MelonGame]`.

## 3. Build and run

- **Build** writes to `dist\Debug`, laid out as `Mods\…`.
- **Run** (`F5`) brings the mod, its dependencies and the Noverna agent into the game for this run. MelonLoader has no separate profile folder, so a *Clean* session hides your other mods for the run and backs up `UserData` and the logs, then restores everything.
- The **Game Log** follows `MelonLoader\Latest.log`; errors go to **Problems**.

## 4. IL2CPP specifics

- Game types live in `Il2Cpp…` namespaces (`Il2CppScheduleOne.…`). IntelliSense tells you when a tutorial uses the Mono name.
- The assembly browser shows the interop signatures; **Native** shows what a method really does (ISIL from `GameAssembly.dll`).
- Components of your own need registering: the snippet *Own MonoBehaviour (IL2CPP)* adds `[RegisterTypeInIl2Cpp]` and the pointer constructor.
- Generic Unity calls differ: `bundle.LoadAsset("x").Cast<GameObject>()` instead of `LoadAsset<GameObject>("x")`; snippets pick the right form.

## 5. Work live

The Live tab, Watch panel, console, picker and hot reload work on Mono and IL2CPP. Hot reload unregisters the old melon (`OnDeinitializeMelon`) and registers the new one. The debugger is for Mono games.

Config: MelonPreferences entries (`CreateCategory`/`CreateEntry`) show in the **Config Editor**, including values set while the game runs.

## 6. Dependencies, assets, tests, release

These work as in the BepInEx guide:

- Libraries from GitHub, NuGet or local DLLs (`UserLibs`), checked before start. Declare melon dependencies with `[assembly: MelonAdditionalDependencies]`.
- **Mod Assets** for your own files, the Unity companion project for AssetBundles, **Replace in my mod** for game textures (`ModReplacements.Apply()` after each scene load on IL2CPP).
- **Tests**, **Compatibility** (including *Narrow down a conflict*) and the **Game Update** report after game updates. On IL2CPP the game update report compares signatures only.
- **Check release** before you ship `dist\Release`.
