# ngrok-dotnet

An open-source, high-performance ngrok agent library for **.NET 10+**. This library wraps the official native 🦀 `ngrok-rust` agent core using source-generated P/Invoke (`LibraryImport`) and exposes an idiomatic, asynchronous API mirroring `ngrok-javascript`.

## 🚀 Quickstart Example

```csharp
using Ngrok.Agent;

var session = await new NgrokSessionBuilder()
    .Authtoken("your_ngrok_auth_token")
    .ConnectAsync();

var tunnel = await session.HttpEndpoint()
    .Domain("my-app.ngrok.dev")
    .ListenAsync();

Console.WriteLine(\$"Tunnel active at: {tunnel.Url}");
await Task.Delay(Timeout.Infinite);
```

## 🏗️ Core Architecture & Development

This project uses a small native Rust bridge to talk to the official `ngrok` library.

### Compiling Native Assets Locally
To develop and run integration tests locally, you need to compile the native C-FFI bridge:
1. Ensure the Rust toolchain (`cargo`) is installed.
2. Navigate to the bridge directory: `cd src/native_bridge`
3. Compile the release binary: `cargo build --release`

The C# project is set up to find the `.dll`, `.so`, or `.dylib` file inside `src/native_bridge/target/release/` automatically during local development.

## 🧪 Testing Approach (TDD)
We build features using a strict **Test-Driven Development (TDD)** loop:
* **Unit Tests (`/tests`)**: Run instantly without external network requests or compiled native binaries. The underlying P/Invoke engine is isolated via an internal `INgrokNativeApi` interface and substituted using `FakeNgrokNativeApi`.
* **Integration Tests**: Hit the live ngrok staging environment. These require an environment variable named `NGROK_AUTHTOKEN` to be configured locally.