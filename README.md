# .NET SDK for ngrok 

[![MIT licensed][mit-badge]][mit-url]
[![Apache-2.0 licensed][apache-badge]][apache-url]
[![Continuous integration][ci-badge]][ci-url]

[mit-badge]: https://img.shields.io/badge/license-MIT-blue.svg
[mit-url]: https://github.com/tomdenosix/ngrok-dotnet/blob/main/LICENSE-MIT
[apache-badge]: https://img.shields.io/badge/license-Apache_2.0-blue.svg
[apache-url]: https://github.com/tomdenosix/ngrok-dotnet/blob/main/LICENSE-APACHE
[ci-badge]: https://github.com/tomdenosix/ngrok-dotnet/actions/workflows/ci.yml/badge.svg
[ci-url]: https://github.com/tomdenosix/ngrok-dotnet/actions/workflows/ci.yml

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