# Getting started: UE4SS Lua mods with Noverna Studio

UE4SS adds Lua scripting to Unreal Engine 4 and 5 games. A Lua mod needs no compiler: it's a folder with `Scripts\main.lua`. This guide uses Escape the Backrooms (UE 4.27) as the example; Palworld (UE 5.1) works the same way.

## 1. Pick the game and install UE4SS

1. In the launcher, select the game. Noverna shows the Unreal Engine version and how the content is packed (Pak or IoStore).
2. Under **Mod framework**, choose **UE4SS** and **Install**. Noverna puts the official release next to the game's `*-Shipping.exe` (`Binaries\Win64`). UE4SS 4.0 or newer is recommended: only then can test sessions run from a profile outside the game folder.
3. Some games need a game configuration for UE4SS (memory layouts, signatures). If one exists, the framework card says **Game configuration available**; **Install into game** adds it. They come from the public [noverna-game-configs](https://github.com/NovernaLabs/noverna-game-configs) repository and are checked against their checksum.

> Don't mod games with anti-cheat online. Noverna warns before the first start of such a game.

## 2. Create the project

**New project** → game, **UE4SS**, template:

- *Lua mod*: logs when it loads and hooks a game function,
- *Lua mod + Key bind*: runs code when you press a key,
- *Lua mod + Config*: keeps settings in `config.lua` so players can change them.

Noverna writes the `.novproject` and `Scripts\main.lua`. There's no `.csproj` and no .NET SDK involved. The first line of `main.lua` (`-- Name 1.0.0 by Author`) follows the version and metadata of the project (**Set Version**, tab **Mod Metadata**).

Code completion and checks come from the Lua language server; Project Health offers to install it. It knows Lua 5.4, the UE4SS API and the modules UE4SS brings (`UEHelpers` …).

## 3. Know the game's types

Lua completion gets much better with the game's own classes:

1. Open **Game Types** (command palette or Project Health) and click **Dump types**.
2. Noverna starts the game once in its own profile with a small dumper mod. UE4SS writes the Lua types and the property mappings (`.usmap`), and Noverna keeps them in its cache. Nothing is written into the game folder.
3. Completion now knows classes like `APlayerController`. The **Game Types** tab lets you search classes, fields and functions, and copy a hook path such as `/Script/Engine.PlayerController:ClientRestart`.

Every build checks the targets of `RegisterHook`, `NotifyOnNewObject` and `FindFirstOf` against the dump. A missing class or function shows up as a warning (NVL101, NVL102). After a game update, dump again: hooks that point into the void show up right away.

## 4. Build and run

- **Build** copies `Scripts\` and an `enabled.txt` to `dist\Debug\<Project>\Binaries\Win64\ue4ss\Mods\<Name>`, laid out like the game.
- **Run** (`F5`) starts a session. With UE4SS 4, **Clean** runs from a profile outside the game: only the mods that come with UE4SS plus yours. **With my mods** runs in the game folder next to your installed mods; everything is put back afterwards, even after a crash.
- The **Game Log** follows `UE4SS.log`. Lua errors land in **Problems** with the file and line in your project (Lua shortens the paths in the log; Noverna maps them back). A syntax error means the mod didn't start; a runtime error tells you which hook failed. Typical errors come with a hint.
- The **Session Timeline** shows when UE4SS started, when your mod started and every reload.

## 5. Hot reload

While the game runs, **save a Lua file** in `Scripts\`: Noverna builds the mod and brings only the changed files into the running session. UE4SS reloads just your mod, without restarting the game, and Noverna confirms it once it's in the log. `Ctrl+Alt+R` does the same by hand.

The mod's state starts fresh after a reload; hooks are registered again by your `main.lua`.

## 6. Browse the game's assets

The **Asset Browser** reads the game's Pak and IoStore files (read-only):

- Packages are listed with the class of their main asset (`Texture2D`, `StaticMesh`, `World`, `DataTable` …), grouped by mount point (`/Game`, `/Engine`, plugins). The first opening indexes the packages; after that they come from the cache.
- Inspector and JSON view of the properties, texture preview, static and skeletal meshes in 3D, sounds (OGG/PCM).
- **Copy as code** gives the object path for Lua, e.g. `StaticFindObject("/Game/Maps/Level0.Level0")`.
- UE5 games often store properties without names. Then the browser needs the mappings from step 3; without them it shows files and names only.
- Encrypted Paks ask for the game's AES key. You bring the key yourself; Noverna keeps it on this PC only, never in the project.

Export is for local analysis only. Game assets can't ship with your mod, and the release check flags unchanged exports.

## 7. Release

**Check Release** (command palette) looks at `dist\Release` before you ship it: `main.lua` must be there, only your mod's own folder goes in, and no files of UE4SS itself (DLL, settings, its own mods, type dumps). Record the environment in Project Health so the `.novproject` says which UE4SS version your mod needs at least.

## Not covered yet

Pak and Blueprint mods (`LogicMods`), UE4SS C++ mods, a Lua debugger and live inspection of the running game are planned for later. For Blueprint mods you still need the Unreal Editor of the game's engine version.
