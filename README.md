# Graphics & Rendering Learning Notes

A project-based learning repository for real-time computer graphics, built around concepts I implemented while developing **AuroraShader** and my OpenGL/Vulkan rendering projects.

The goal is not just to keep code that works, but to document **why each rendering technique works**, the math behind it, where it appears in the pipeline, and how to debug it.

## Learning Path

1. Rendering Pipeline
2. GLSL Vectors, UVs, Colors, Texture Sampling
3. Post Processing
4. Coordinate Spaces & Matrices
5. G-buffer & Deferred Rendering
6. Normals & Lambert Lighting
7. Lightmap Composition
8. Depth & Position Reconstruction
9. Shadow Mapping
10. PCF Soft Shadows
11. Debugging & Validation
12. Custom Sky Foundations
13. Review Plan

See [`lessons/`](lessons/) for the full notes.

## Project Mapping

These notes are grounded in actual renderer milestones:

- **AuroraShader Milestone 1** — Color processing / post-processing
- **AuroraShader Milestone 2** — G-buffer normals + deferred directional lighting
- **AuroraShader Milestone 3A** — Basic directional shadow mapping
- **AuroraShader Milestone 3B** — 3×3 PCF soft shadows
- **Milestone 4A** — Custom sky (learning notes prepared; implementation still evolving)

## Reference Commits

- Milestone 2: `c181095102991f1627d10c12418e5a9a01925fe9`
- Milestone 3A: `bb7a5d63a76f426dd3af75d413d7dea5a90b5d15`
- Milestone 3B: `5aa154dbce823e954f63d25f91f3acbb9566c9c9`

## How to Use This Repository

For every topic:

1. Read the lesson.
2. Open the corresponding renderer code.
3. Explain the math in your own words.
4. Use the debug view to inspect intermediate data.
5. Reproduce the idea in a smaller OpenGL/Vulkan experiment later.

## Exercises

See [`EXERCISES.md`](EXERCISES.md).

## Glossary

See [`GLOSSARY.md`](GLOSSARY.md).

## Future Topics

Planned additions:

- Custom Sky
- Distance / Height Fog
- HDR & Bloom
- Water Rendering
- SSAO
- SSR
- Volumetric Lighting
- PBR
- Vulkan rendering architecture
