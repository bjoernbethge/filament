# Engine Roadmap

Filament is a renderer core first. The roadmap below outlines the engine-style subsystems that sit
above Filament so it can be used in workflows closer to Unreal or Godot, without expanding the
renderer itself.

## Planned engine subsystems

- Scene graph / ECS: transforms, hierarchy, culling, component authoring.
- Input: cross-platform input mapping, controllers, touch, and gestures.
- Physics: rigid bodies, character controllers, and queries.
- Scripting: extensible runtime scripting for gameplay and tools.
- Editor: scene authoring, prefab/asset workflows, and profiling views.
- Asset pipeline: importers, cooking, dependency tracking, build cache.
- Audio: spatial audio, mixer, and streaming.
- Networking: replication primitives and transport integrations.
- Tooling: build/deploy automation, debugging, and inspection utilities.

## Feature parity checklist (Unreal 5.x / Godot 4.x)

### Unreal Engine 5.4 highlights

- [ ] Nanite tessellation and spline mesh workflows ([UE 5.4 release notes](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5.4-release-notes?application_version=5.4)).
- [ ] Movie Render Graph and render layers for cinematic output ([UE 5.4 announcement](https://www.unrealengine.com/en-US/blog/unreal-engine-5-4-is-now-available)).
- [ ] Modular and layered Control Rig improvements ([UE 5.4 release notes](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5.4-release-notes?application_version=5.4)).
- [ ] Production-ready Motion Matching ([UE 5.4 release notes](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5.4-release-notes?application_version=5.4)).
- [ ] Temporal Super Resolution upgrades ([UE 5.4 release notes](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5.4-release-notes?application_version=5.4)).

### Godot Engine 4.3 highlights

- [ ] Interactive music resources ([Godot 4.3 release](https://godotengine.org/releases/4.3/)).
- [ ] 2D physics interpolation ([Godot 4.3 release](https://godotengine.org/releases/4.3/)).
- [ ] Visual Shader editor overhaul ([Godot 4.3 release](https://godotengine.org/releases/4.3/)).
- [ ] Direct3D 12 rendering backend ([Godot 4.3 release](https://godotengine.org/releases/4.3/)).
- [ ] Single-threaded web export improvements ([Godot 4.3 release](https://godotengine.org/releases/4.3/)).
- [ ] Native FBX (ufbx) importer ([Godot 4.3 release](https://godotengine.org/releases/4.3/)).

### Godot Engine 4.2 highlights

- [ ] AnimationMixer-based animation workflow updates ([Godot 4.2 release](https://godotengine.org/article/godot-4-2-arrives-in-style/)).
- [ ] TileMap/TileSet performance and UX improvements ([Godot 4.2 release](https://godotengine.org/article/godot-4-2-arrives-in-style/)).
