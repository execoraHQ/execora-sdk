# @quonexai/sdk
![execora-sdk](assets/background.jpeg)
Execora is an AI-powered execution and automation layer designed to handle on-chain tasks, routing, and scalable utility infrastructure.

his enables bots, agents, infra tools, and utility services to run tasks such as swaps, transfers, stake flows, routing and batch execution.

---

## ✨ Features

- 🧩 Modular task execution
- 🤖 AI-assisted routing (pluggable)
- 🔁 Task queue management
- 🔗 Multi-chain executor adapters (Solana + EVM base)
- ⚡ Low-latency execution model
- 🧱 Simple integration for bots/agents
- 🔌 Job automation ready
- 🏗 Designed for scalable infra + utility tokens

---

## 📦 Installation

```bash
npm install execora-sdk
```
## 🚀 Quick Example

```typescript
import { Execora } from "execora-sdk";

const exec = new Execora({
  apiKey: process.env.EXECORA_KEY
});

const result = await exec.execute({
  chain: "evm-mainnet",
  action: "swap",
  params: {
    from: "ETH",
    to: "USDC",
    amount: "0.5"
  }
});

console.log(result);

## 🧩 Core Concepts
```typescript
{
  chain: "evm-mainnet",
  action: "swap",
  params: {...}
}


### PDA Helpers

#### `deriveRequestPda(programId: PublicKey, userPubkey: PublicKey, requestNonce: bigint): [PublicKey, number]`

Derives the request PDA for a given user and request nonce.

**Seeds:** `["qnx_req", userPubkey, requestNonce(u64 LE)]`

#### `deriveResponsePda(programId: PublicKey, requestPda: PublicKey): [PublicKey, number]`

Derives the response PDA for a given request PDA.

**Seeds:** `["qnx_res", requestPda]`

### Codec Functions

#### `encodeSubmitLLMRequest(data: SerializedLLMRequest): Buffer`

Encode a request to Borsh binary format.

#### `decodeSubmitLLMRequest(buffer: Buffer): SerializedLLMRequest`

Decode a request from Borsh binary format.

#### `encodeSubmitLLMResponse(data: SerializedLLMResponse): Buffer`

Encode a response to Borsh binary format.

#### `decodeSubmitLLMResponse(buffer: Buffer): SerializedLLMResponse`

Decode a response from Borsh binary format.

### Validation Functions

#### `assertNonEmptyString(name: string, value: string): void`

Throws `ValidationError` if the string is empty.

#### `assertMaxLen(name: string, value: string, max: number): void`

Throws `ValidationError` if the string exceeds max length.

#### `assertU32(name: string, value: number): void`

Throws `ValidationError` if the value is not a valid u32 (0 to 2^32 - 1).

#### `toU16Milli(name: string, value: number): number`

Converts a float (0.0 to 1.0) to u16 millis (0 to 1000).  
E.g., `0.7 => 700`, `0.5 => 500`.

#### `fromU16Milli(milli: number): number`

Converts u16 millis to a float.  
E.g., `700 => 0.7`.

### Error Classes

```typescript
export class QuonexSDKError extends Error;
export class ValidationError extends QuonexSDKError;
export class SerializationError extends QuonexSDKError;
export class DeserializationError extends QuonexSDKError;
```

## Development

### Install Dependencies

```bash
npm install
```

### Build

```bash
npm run build
```

Outputs to `dist/` with `.mjs`, `.cjs`, and `.d.ts` files.

### Type Check

```bash
npm run typecheck
```

### Lint

```bash
npm run lint
```

Enforces strict TypeScript and ESLint rules.

### Test

```bash
npm run test
```

All tests run locally without any cluster or CLI tools.

### Watch Mode

```bash
npm run dev
```

Rebuilds on file changes.

### Format

```bash
npm run format
```

Runs Prettier on all source and test files.

## Testing

The SDK includes comprehensive unit tests for:

- **Codec Roundtrips** – Encode/decode invariants for both instruction types
- **Discriminators** – Correct discriminator bytes
- **PDA Derivation** – Deterministic PDA generation and validation
- **Instruction Building** – Account ordering, validation, and determinism
- **Edge Cases** – Unicode, large payloads, max values, boundary conditions

Run tests with:

```bash
npm run test          # Run all tests
npm run test:ui       # Open interactive UI
```

All tests are 100% deterministic and require no external services.

## Constraints & Validation

The SDK enforces the following constraints:

| Field               | Constraint                      |
| ------------------- | ------------------------------- |
| `model`             | Max 64 characters               |
| `prompt`            | Max 8000 characters             |
| `maxTokens`         | Valid u32 (0 to 2^32 - 1)      |
| `temperature`       | 0.0 to 1.0 (scaled to u16 millis) |
| `topP`              | 0.0 to 1.0                     |
| Metadata key        | Max 256 characters              |
| Metadata value      | Max 4096 characters             |

Attempting to create an instruction with invalid data throws a `ValidationError` with a clear message.

## Configuration

### Custom Program ID

By default, the SDK uses `DEFAULT_PROGRAM_ID`. To use a custom program:

```typescript
import { createSubmitLLMRequestIx } from "@quonexai/sdk";
import { PublicKey } from "@solana/web3.js";

const myProgramId = new PublicKey("YOUR_PROGRAM_ID_HERE");

const instruction = createSubmitLLMRequestIx({
  programId: myProgramId,
  // ... rest of args
});
```

## Borsh Encoding Details

The SDK uses a custom Borsh codec optimized for TypeScript ESM:

- **Discriminators:** u8 (1 for request, 2 for response)
- **Integers:** Little-endian (LE) byte order
  - u8: 1 byte
  - u16: 2 bytes
  - u32: 4 bytes
  - u64: 8 bytes
- **Strings:** u32 length prefix (LE) + UTF-8 bytes
- **Options:** u8 tag (0 = None, 1 = Some) + optional payload
- **Vectors:** u32 length (LE) + repeated elements

This format is compatible with Borsh and Anchor on-chain deserialization.

## License

MIT

## Support

For issues, questions, or contributions, please visit the [QuonexAI SDK repository](https://github.com/execoraHQ/execora-sdk).
