# Vortex Portable Instance Solution

## Concepts

**Why use portable instace?**

- System-independent
- User-independent

Vortex have no options to set portable instance.

After viewing the source code of Vortex, I found there are some **CLI arguments** could be useful.

## By Using CLI args

Recommend. Tested version: **Vortex 1.3.22.0**.

- Copy *user data* of `Shared` mode

  Install Vortex then open it. Suppose the installation location is `D:\TES\Vortex\`.
  
  Open *Settings -> Vortex -> Multi-User Mode*, set it to `Shared` to initialize the shared user data.
  
  Then close Vortex, copy the shared user data `C:\ProgramData\Vortex` to another directory.
  Suppose copy to `D:\TES\Vortex_User_Data`.
  
- Create a `.bat` file contains correct CLI arguments

  Let's name it **start_Vortex_Portable.bat**. 
  
  Then add the following content, change the value of `Vortex_Path`, `User_Data_Path` to yours, and save it.
  ```bat
  set Vortex_Path=D:\TES\Vortex\Vortex.exe
  set User_Data_Path=D:\TES\Vortex_User_Data
  %Vortex_Path% --shared --user-data %User_Data_Path%
  pause
  
  ```
  
- Run `start_Vortex_Portable.bat`.

## Additional Importance

- Must add **`--shared`** flag, or Vortex will use `Per-User` at you next start.

- It **seems** that Vortex use `%APPDATA%\Vertox` to store whether use `Shared` mode.

	- Don't delete `%APPDATA%\Vertox`.
	- Switch system or user, may need reset to `Shared` mode.
- Recommend manage mods manually, and **disable Vortex automatic update**.

## Vortex Use Case

*Vortex*, *Mod Organizer 2*, any others mod managers, they do:
- merge some files to one directory
- solve conflicts
- aboves operations can be recovered fastly
- ...

Requirements: JavaScript Programming.

For SSE, manage some mods, utilities, plugins that must be installed to SSE root directory, such as Creation Kit, SKSE64, ENB.

For other games and programs, like these features.



----

----

----

# The below does nothing.

Some content of `src/util/commandLine.ts`:
```ts
const commandLine = program
  .command('Vortex')
  .version(version)
  .option('-d, --download [url]', 'Start downloadling the specified url '
                                + '(any supported protocol like nxm:, https:, ...).')
  .option('-i, --install [url]', 'Start downloadling & installing the specified url '
                                + '(any supported protocol like nxm:, https:, ...).')
  .option('-g, --get [path]', 'Print the state variable at the specified path and quit. '
                            + 'For debugging')
  .option('-s, --set [path]=[value]', 'Change a value in the state. Please be very careful '
                                    + 'with this, incorrect use will break Vortex and you may '
                                    + 'lose data', assign)
  .option('--user-data [path]', 'Starts Vortex with a custom directory for the user data. '
                                + 'Only use if you know what you\'re doing.')
  .option('--start-minimized', 'Starts Vortex in the task bar')
  .option('--game [game id]', 'Starts Vortex with a different game enabled')
  .option('--del [path]', 'Remove a value in state')
  .option('--run [path]', 'Execute the js program instead of Vortex itself.')
  .option('--report [path]', 'Send an error report. For internal use')
  .option('--restore [path]', 'Restore a state backup')
  .option('--shared', 'Used in conjunction with set, get or del, this will access the database'
                                     + 'in the shared location instead of the per-user one')
  .option('--max-memory [size in MB]', 'Maximum amount of memory Vortex may use in MB '
                                     + '(defaults to 4096)')
  .option('--inspector', 'Start Vortex with the chrome inspector opened')
  // allow unknown options since they may be interpreted by electron/node
  .allowUnknownOption()
  .parse(argv || []).opts() as IParameters;
```

# Multi-User Mode Concepts

In menu *Settings -> Vortex -> Multi-User Mode*, has two options:
- Per-User
- Shared

> **Caveat**
>
> Even using *Shared* mode, Vortex will use `%APPDATA%\Vertox` to store some info.
>
> Such as the options of `Multi-User Mode`, so don't delete `%APPDATA%\Vertox`.


# Instance Relative Concepts

## User Data Directory

If using `Shared` mode, the user data of Vortex located at `C:\ProgramData\vortex`.

Two files contains instance UUID.

**./downloads/__vortex_downloads_folder** is JSON file.
```json
{"instance":"fa030b1a-3adf-4b47-a52e-ef6b252dc988"}
```

**./state.v2/000059.ldb** is binary file.
```text
nceId(other content)"fa030b1a-3adf-4b47-a52e-ef6b252dc988"(other content)
```

## Staging Path

Two files contains instance UUID.

**__vortex_staging_folder** is a JSON file.
```json
{"instance":"fa030b1a-3adf-4b47-a52e-ef6b252dc988","game":"root-of-skyrimse"}
```

**vortex.deployment.msgpack** is a binary file.
```text
(other content)fa030b1a-3adf-4b47-a52e-ef6b252dc988ersion(other content)
```

## Target Path

In Target Path, has **vortex.deployment.json**.
```json
{
  "instance": "fa030b1a-3adf-4b47-a52e-ef6b252dc988",
  "version": 1,
  "deploymentMethod": "hardlink_activator",
  "deploymentTime": 1608414614243,
  "stagingPath": "D:\\TES\\SE_Vortex_mods",
  "targetPath": "D:\\TES\\SkyrimSE",
  "files": []
} 
```

