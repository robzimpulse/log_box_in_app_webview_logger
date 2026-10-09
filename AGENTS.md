# log_box_in_app_webview_logger Context

## Purpose:
This repository contains a specialized logger for the LogBox ecosystem designed to capture and display events from the `flutter_inappwebview` library. It provides real-time monitoring of WebView lifecycle events, console messages, network errors, and JavaScript executions within the app's web views.

## Key Components:
- **lib/src/observer/in_app_webview_observer.dart**: The primary controller that translates WebView callbacks (like `onLoadStart`, `onProgressChanged`, `onConsoleMessage`) into LogBox `WebviewEntryModel` events.
- **lib/src/model/webview_entry_model.dart**: The top-level data model representing a single WebView session and its associated metadata (URL, HTML content, errors).
- **lib/src/model/webview_entry_model_log.dart**: A granular model for individual events within a WebView session (e.g., a specific console message or progress update).
- **lib/src/enum/enum.dart**: Defines the `WebviewEvent` types supported by the logger.
- **lib/src/screen/**: Contains specialized UI components for visualizing WebView logs, including event timelines and content inspectors.

## Dependencies:
- **flutter_inappwebview**: The external library this package monitors.
- **log_box**: The core internal module used for its `Storage` and base log abstractions.
- **uuid**: Used for generating unique identifiers for WebView sessions.
- **json_annotation**: Used for generating serialization logic for the WebView models.

## Local Conventions:
- **Observer Pattern**: Developers should instantiate `InAppWebviewObserver` and call its methods within the relevant `InAppWebView` callbacks to capture events.
- **Event-Driven Logging**: Unlike standard logs, WebView logs are often long-lived sessions; the `WebviewEntryModel` supports a list of `events` to track a session's history over time.
- **Detailed Metadata**: The observer is designed to capture not just the event type, but also rich "extra" data (like resource requests or console message levels) to aid in debugging.
- **Automated Serialization**: Models in `lib/src/model/` use `json_serializable` for consistency with the rest of the LogBox ecosystem.

## Development:
- **Commands**: Use the `Makefile` (`make` lists targets). `make analyze` and `make format-check` must pass — CI enforces both (infos are fatal).
- **Code Generation**: Run `make generate` after modifying `@JsonSerializable` models; commit the `.g.dart` files.
- **Releasing**: Bump `version:` in `pubspec.yaml` and add a matching `## <version>` section to `CHANGELOG.md` in the same PR; merging creates tag `v<version>` via `release.yaml`.

## Known Pitfalls:
- **Cross-Repo Dependency Bumps**: `log_box` is pinned by git tag (`ref: v<version>`), so CI never sees an unreleased core change. For a shared-constraint bump (e.g. rxdart), land and release it in [log_box](https://github.com/robzimpulse/log_box) first, then update `ref:` and the constraint here in its own PR. See core's `AGENTS.md` → "Cross-Repo Dependency Bumps".
- **Pushing Workflow Changes**: Pushing files under `.github/workflows/` requires a GitHub token with the `workflow` scope. If a push is rejected with "refusing to allow an OAuth App to create or update workflow", run `gh auth refresh -h github.com -s workflow` and make git use that token (`gh auth setup-git`) — a stale macOS Keychain token will otherwise keep failing.
