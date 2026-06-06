# VoidStrap — what changed and how to build

This is a rebrand of **Bloxstrap** (MIT, by pizzaboxer) into **VoidStrap**, plus a new
multi-instance launching feature. The UI is unchanged, so it looks exactly like Bloxstrap.

## What was changed

### Rebrand
- Every `Bloxstrap` identifier (namespaces, classes, the `ProjectName` constant, assembly
  name, internal mutex prefixes, the `*CustomBootstrapper` protocol key) is now `VoidStrap`.
- Renamed files/folders: `Bloxstrap/` → `VoidStrap/`, `Bloxstrap.sln/.csproj/.ico`,
  `BloxstrapPage`, `BloxstrapViewModel`, `IconBloxstrap*`, `Images/Bloxstrap*.png`, `BloxstrapRPC`.
- **Left untouched on purpose:** `ROBLOX_singletonMutex` / `ROBLOX_singletonEvent` and
  `RobloxPlayerBeta` — those are Roblox's own identifiers; renaming them breaks launching
  and multi-instance.
- The original author's URLs (`bloxstraplabs.com`, `bloxstraplabs/bloxstrap`) were preserved
  where they're just wiki/help links so nothing dead-links.
- `LICENSE` still carries the original `Copyright (c) 2022 pizzaboxer` — this is required by
  the MIT license. Keep it.

### ⚠️ One thing you MUST edit
Open `VoidStrap/App.xaml.cs` and set your fork's repo (drives the auto-updater):
```csharp
public const string ProjectRepository = "YOUR_USERNAME/VoidStrap";
public const string ProjectDownloadLink = "https://github.com/YOUR_USERNAME/VoidStrap/releases/latest";
public const string ProjectHelpLink     = "https://github.com/YOUR_USERNAME/VoidStrap/wiki";
public const string ProjectSupportLink  = "https://github.com/YOUR_USERNAME/VoidStrap/issues/new";
```

### Multi-instance launching (new feature)
- New setting `MultiInstanceLaunching` in `Models/Persistable/Settings.cs`.
- New class `Utility/MultiInstanceLock.cs` — owns Roblox's singleton mutex/event so multiple
  clients don't shut each other down.
- Wired into `Watcher.cs`: when the setting is on, the lock is acquired for the whole session
  and released when the watcher exits.
- New toggle in the Behaviour settings page ("Allow multiple Roblox instances").

How it works in practice: launch a game through VoidStrap (instance #1). Its watcher process
holds the singleton mutex for as long as that client runs. Launch again → instance #2 opens
instead of being blocked. Repeat for as many as you want.

## Building

You need **Windows**, **Visual Studio 2022** with the **.NET Desktop** workload, and **git**.

1. Initialize the UI submodule (it ships empty in a plain download):
   ```
   git submodule update --init --recursive
   ```
   (If you don't have it as a submodule, clone `bloxstraplabs/wpfui` into the `wpfui/` folder.)
2. Open `VoidStrap.sln` in Visual Studio.
3. Set your repo in `App.xaml.cs` (see above).
4. Drop in your own branding art if you want (replace `VoidStrap.ico`, `Resources/IconVoidStrap.ico`,
   `Images/VoidStrap*.png`). Same dimensions as the originals.
5. Build → Release. To produce a single distributable exe, from the repo root:
   ```
   dotnet publish -p:PublishSingleFile=true -r win-x64 -c Release --self-contained false .\VoidStrap\VoidStrap.csproj
   ```

## Note on fair use
Multi-instance itself is fine for testing or playing different games at once. Using it to run
automated / bot accounts is what gets accounts banned — don't.
