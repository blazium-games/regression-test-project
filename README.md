# Blazium regression test project

Fork of [godotengine/regression-test-project](https://github.com/godotengine/regression-test-project) adapted for [Blazium Engine](https://blazium.app) CI.

It aims to exercise as many ClassDB types and states as practical, stay easy to maintain, and produce reproducible results under ASAN/UBSAN editor builds.

## Blazium changes

- Exclude Blazium-only ClassDB types that require a live editor/runtime session (`JustAMCP*`, `Lua*`, `AssetTag*`, `Semantic*`, embedding backends, etc.) from the FunctionExecutor fuzzer and Nodes smoke scene.
- Used by Blazium's `.github/actions/godot-project-test` workflow action.

## Basic information

This project contains several scenes and `Start.tscn` (default) which opens every other scene.

Scene lists live in `Autoload/Autoload.gd`.

You can set how long the project runs from the command line by appending seconds, e.g. `blazium 40`.

At first `all_in_one` scenes open, execute `_ready`, then exit.

The available time (default 25s) is divided equally between each scene in `alone_steps`. Then each scene is created and, after a slice of time, deleted so the next scene can run.

This project runs in Blazium CI with Address and Undefined sanitizers (`scons use_asan=yes use_ubsan=yes`).

## Searching for a malfunctioning scene

### Logs

```
Changed scene to res://Rendering/Lights2D/Lights2D.tscn
Test is running now 35 seconds
Changed scene to res://Rendering/Lights3D/Lights3D.tscn
##### CRASH #####
```

That usually implicates:

- `Lights3D.tscn` — crash when the scene started
- `Lights2D.tscn` — crash when the scene was removed

### Autoload

Comment out entries in `alone_steps` / `all_in_one`, or adjust `time_to_show`, to narrow which scenes run.

### File system

Run individual scenes from the FileSystem dock to isolate a failure.

## Safe fuzzer

`AutomaticBugs/FunctionExecutor.tscn` fuzzes ClassDB methods with fixed (non-random) arguments. When the engine crashes, logs usually show the class, method, parameters, and a copy-pasteable GDScript line.

## License

MIT — see `LICENSE`. Upstream project: [godotengine/regression-test-project](https://github.com/godotengine/regression-test-project).
