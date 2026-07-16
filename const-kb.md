# Runtime Const Knowledge Base

`runtime-const` owns shared vocabulary and numeric constants for CarbonEngineJS.

## Purpose

This package provides stable constants that can be shared by:

- `format-*` packages when emitting GPU-free semantic payloads.
- `runtime-resource` when interpreting payloads and resource intent.
- `engine-*` packages when mapping payloads to backend APIs.
- tools and tests that need canonical media, graphics, audio, shader, D3D, or
  backend names.

## Boundaries

`runtime-const` owns:

- canonical string tokens, such as pixel formats and color spaces.
- small helpers for normalization and classification.
- numeric mirrors of external enums where useful, such as DXGI/D3D values.
- mapping tables between shared constants and backend constants.

`runtime-const` does not own:

- resource lifecycle, cache, source reads, or loader dispatch.
- format parsing or byte decoding.
- format-container internals such as DDS header offsets, FOURCC parsing,
  PNG chunk handling, WAV chunk layouts, or MP4 box parsing.
- class hydration or runtime object population.
- WebGPU/WebGL resource creation.

## Dependency Rule

`runtime-const` should stay dependency-light and pure JavaScript.

Preferred direction:

```text
format-*          may emit matching strings, optionally import runtime-const
runtime-resource  may import/re-export runtime-const
engine-*          imports runtime-const for backend mapping
runtime-const     imports no runtime-resource or engine packages
```

Format packages are allowed to remain standalone by emitting plain strings that
match this package's constants.

## Initial Domains

- media and payload types
- graphics pixel formats, color spaces, texture dimensions
- audio sample formats and channel layouts
- shader stages and shader models
- D3D/DXGI enum mirrors
- WebGPU mapping helpers

## Format-Specific Constants

Container-specific constants live with their format package.

Examples:

- DDS magic numbers, header offsets, pixel format flags, caps bits, FOURCC
  values, and DX10 header parsing belong to `format-dds`.
- PNG chunk names and filter ids belong to `format-png`.
- WAV RIFF chunk ids belong to `format-wav`.

`runtime-const` may still expose general constants that those formats reference,
such as canonical `PixelFormat` strings or generic `DxgiFormat` numeric enum
values. The parser-specific interpretation remains in the format package.

## Payload Example

```js
{
    payloadType: "texture",
    sourceFormat: "dds",
    pixelFormat: "bc7-rgba-unorm",
    colorSpace: "srgb",
    dimension: "2d",
    isCompressed: true
}
```

The payload is still GPU-free. Engine packages decide whether and how to create
backend resources from it.
