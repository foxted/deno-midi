# deno_midi

⚠️ This is a work in progress and may be subject to breaking changes until it
hits 1.0. ⚠️

A Deno wrapper for the [RtMidi](https://github.com/thestk/rtmidi) C++ library⚠️
that provides realtime MIDI I/O.

It uses pre-built binaries of the 6.0.0 version of the library for Windows,
Linux and MacOS (x64 and arm64), so no need to install any dependencies. They're
built from my fork of the RtMidi library that adds some extra features.

It uses Deno FFI to call the C++ library. The bindings have been generated
thanks to the great help from [Aapo Alasuutari](https://github.com/aapoalas) and
his [libclang_deno](https://github.com/aapoalas/libclang_deno) bindings
generator. He's also the author of the
[denonomicon](https://denonomicon.deno.dev/) which is a great resource for FFI.

## Usage

Since it uses FFI, it should be run with the `--unstable` flag.

### Basic check

```ts
import { midi } from "https://deno.land/x/deno_midi/mod.ts";

console.log(midi.getVersion()); // Should print 6.0.0
```

### Midi Input

```ts
import { midi } from "https://deno.land/x/deno_midi/mod.ts";

const midi_in = new midi.Input();
// List the available ports.
console.log(midi_in.getPorts());
midi_in.openPort(0); // Open the first available port.
// Set a callback to receive the messages. It keeps the program alive.
midi_in.onMessage(({ deltaTime, message }) => {
  console.log(`Message received at ${deltaTime} : ${message}`);
});
// Stop the callback when you're done.
midi_in.offMessage();
// Close the port.
midi_in.closePort();
// It's still possible to open an other port, otherwise you can free the device.
midi_in.freeDevice();
```

### Midi Output

The API is very similar, except you can send MIDI messages.

```ts
import { midi } from "https://deno.land/x/deno_midi/mod.ts";

const midi_out = new midi.Output();
console.log(midi_out.getPorts());

midi_out.openPort(0);

// Send a note on.
midi_out.sendRawMessage([0x90, 0x3C, 0x7F]);
// Send a note off after 1 second.
setTimeout(() => {
  midi_out.sendRawMessage([0x80, 0x3C, 0x2F]);
}, 1000);
```

A complete example can be found in the example folder.
