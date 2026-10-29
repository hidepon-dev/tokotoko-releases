# Privacy Policy for TokoToko

Last updated: October 29, 2026

TokoToko ("the Application") is an ultra-lightweight desktop mascot designed and maintained by hidepon. We are committed to respecting and protecting your privacy. This Privacy Policy explains how information is handled when you use the Application.

## 1. Information Collection and Storage
- **No Personal Data Collected:** The Application does not collect, track, log, or transmit any personally identifiable information (PII) or sensitive personal data.
- **Local Operation Only:** All core operations—including mascot animations, IPC communication (via local sockets / named pipes), and character package imports—run strictly on your local machine.
- **Local Configuration & Storage:** Application settings, character configurations, and activity statistics are stored locally on your device in your user profile directory (`~/.tokotoko/` on macOS/Linux, `%USERPROFILE%\.tokotoko\` on Windows) and are never uploaded to any external server or cloud service.
- **Local Browser Extension & IPC Communication:** Communication with optional companion tools or browser extensions (such as the TokoToko Chrome Extension) is conducted strictly locally on your machine via standard input/output (stdio) and local Named Pipes / Unix domain sockets. No browsing history, chat prompts, emails, or personal data are collected, logged, or transmitted.
- **No Telemetry / No Analytics:** The Application does not contain any tracking code, telemetry, or behavioral analytics tools.

## 2. Third-Party Sharing and Network Communication
- **No Background Network Traffic:** During normal operation, the Application does not communicate over the internet or send data to external servers.
- **Manual Update Check:** Only when you explicitly click the "Check for Updates" button in the Settings window, the Application makes a read-only HTTPS request to the official GitHub Releases API (`https://api.github.com/repos/hidepon-dev/tokotoko-releases/releases/latest`) to check the latest version number. No personal or device data is transmitted during this check.
- **No Data Sharing:** The Application does not sell, trade, or transfer any user data to third parties.

## 3. Contact
If you have any questions or feedback regarding this Privacy Policy, please contact us via our [GitHub Issues](https://github.com/hidepon-dev/tokotoko-releases/issues).
