# Privacy Policy for TokoToko Gmail Connector

Last updated: November 10, 2026

TokoToko Gmail Connector ("the Extension") is committed to protecting your privacy. This Privacy Policy explains how information is handled when you use the Extension.

## 1. Data Usage Statement
* **Local Notification Purpose Only:** The "TokoToko Gmail Connector" extension accesses your unread email feed (`https://mail.google.com/mail/u/0/feed/atom`) solely for the purpose of triggering local desktop notifications when a new unread email arrives.
* **Minimal Metadata Extraction:** The Extension extracts only the sender's name and email subject of the latest unread message and transmits this information strictly locally to your "TokoToko" desktop application via Chrome's Native Messaging API.
* **No Email Body Access:** The Extension does not read, store, or process the full body content or attachments of your emails.
* **Local State & Preferences:** To prevent duplicate notifications for the same unread email, the Extension stores only the latest notified email identifier locally in your browser using `chrome.storage.local`. Your display and notification language preference (English or Japanese) is stored using `chrome.storage.sync`.

## 2. No External Transmission or Third-Party Sharing
* **No External Servers:** Your email data is **NEVER** transmitted over the internet to external servers, logged remotely, shared with third parties, sold, or used for advertising, analytics, or AI training. All processing happens strictly locally on your computer.

## 3. Contact
If you have any questions about this Privacy Policy, please open an issue on our [GitHub repository](https://github.com/hidepon-dev/tokotoko-releases/issues).
