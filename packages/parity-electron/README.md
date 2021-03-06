# @parity/electron

Control the Parity client from electron.

## Getting Started

```bash
yarn add @parity/electron
```

## Usage

```javascript
import parityElectron, { isParityRunning } from '@parity/electron';

// Optional: override default options
parityElectron({
  cli: myOwnCliObject,
  logger: myCustomLoggerFunction
})

isParityRunning()
  .then(() => ...);
```

## API

#### `parityElectron(options: Object)`

If you don't want to override the default options, there's no need to call this function. Here `options` can have the following fields:

| Option           | Default Value      | Description                                                                                                                                                                |
| ---------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `options.cli`    | `{}`               | An object where key/values are --flags passed to the binary. The `cli` object returned by `yargs` or [`commander.js`](https://github.com/tj/commander.js/) would fit here. |
| `options.logger` | `require('debug')` | A function with the same signature as [`debug`](https://github.com/visionmedia/debug). All logs inside `@parity/electron` will then be logged by this function.            |

#### `fetchParity(mainWindow: BrowserWindow, options: Object): Promise<String>`

Downloads Parity, saves it to Electron's `userData` folder, and returns the path to the downloaded binary once finished.

| Option                  | Type                                                                                         | Description                                                                                                                                |
| ----------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `mainWindow`            | [BrowserWindow](https://github.com/electron/electron/blob/master/docs/api/browser-window.md) | The Electron BrowserWindow in which to download the binary.                                                                                |
| `options.onProgress`    | `Function`                                                                                   | Optional callback that receives a number between 0 and 1 representing the progress of the current download.                                |
| `options.parityChannel` | `String`                                                                                     | Can be `stable`, `beta` or `nightly`. If downloading Parity is needed, determines which version of Parity to download. Defaults to `beta`. |

#### `getParityPath(): Promise<String>`

Returns the path to Parity. It checks (in this order) if Parity is in `$PATH`, in a standard OS location or in Electron's `userData` folder, and returns the first instance of Parity found. The Promise rejects if no Parity instance is found.

#### `defaultParityPath(): Promise<String>`

Returns the path to the Parity path inside Electron's `userData` folder, even if that binary doesn't exist. It's the default download location for [`fetchParity`](#fetchParitymainWindow-BrowserWindow-options-Object-PromiseltStringgt0).

#### `isParityRunning(): Promise<Boolean>`

Resolves to `true` if Parity is currently running, or to `false` if not.

#### `runParity(additionalFlags: Array<String>, onParityError: Function): Promise<Null>`

Spawns a child process to run Parity. If some `cli` flags are passed into the options in `parityElectron`, then those flags will be passed down to Parity itself.

| Option            | Type            | Description                                                                                   |
| ----------------- | --------------- | --------------------------------------------------------------------------------------------- |
| `additionalFlasg` | `Array<String>` | Addtional flags to pass to Parity, listed as an array, to be passed to `child_process.spawn`. |
| `onParityError`   | `Function`      | Callback with `error` as argument when Parity encounters an error.                            |

#### `killParity(): Promise<Null>`

If a Parity process has been spawned with [`runParity`](#runParityonParityError-Function-PromiseltNullgt), then it kills this process. The Promise resolves instantly, there's no guarantee that Parity has been cleanly killed.

#### `deleteParity(): Promise<Null>`

If Parity has been downloaded to Electron's `userData` folder, then it deletes the Parity binary file from that folder.

#### `signerNewToken(): Promise<String>`

Runs `parity signer new-token` and resolves with a new secure token to be used in a dapp. Rejects if no token could be extracted.

#### `checkClockSync(): Promise<Object{isClockSync: boolean, timeDrift: number}>`

Use SNTP to check if the local clock is synchronized; return the time drift.