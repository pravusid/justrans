# Privacy Policy

[한국어](./PRIVACY.ko.md)

Effective date: 2026-10-25

justrans is a browser extension that translates web pages and selected text and summarizes web pages and YouTube captions when the user asks it to. The extension's operator runs no backend, advertising, or analytics service. The data below is processed directly between the user's browser and the services the user selects.

## Data we process

| Data                                                                                                                        | Purpose                                                                                                                                   | Local retention                                                                                                                                                                                                | Sent to                                                                                                                                                                              |
| --------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Selected text, immersive translation target and surrounding context blocks, page text, YouTube captions                     | Producing the translation or summary the user requested                                                                                   | Translation source text and results are kept in the translation cache in `browser.storage.session` for up to 1 hour (see "Retention and deletion"). Summary input and output stay in memory only while running | The translation or summary service chosen for each feature (see "Data sent to external services"). Chrome Translator processes on the device. YouTube is also used to fetch captions |
| Translation and summary prompts written by the user                                                                         | Applying the user's instructions to translation and summary requests                                                                      | Kept in extension-only IndexedDB until the user changes them or removes the extension                                                                                                                          | Included in translation and summary requests that use an LLM service                                                                                                                 |
| LLM service API keys                                                                                                        | Authenticating model listing, translation, and summary requests                                                                           | `browser.storage.session` by default. If the user chooses persistent storage, encrypted with an extension-only key and kept in extension-only IndexedDB. That key can optionally be locked with a password     | Sent only to the endpoint of the service the key was saved for                                                                                                                       |
| Service, model, endpoint, target language, UI and immersive translation settings, site domain list entered by the user      | Configuring features and restoring user settings                                                                                          | Kept in extension-only IndexedDB until the user changes them or removes the extension                                                                                                                          | Only the values a request needs, such as the chosen model and target language, are sent to that service                                                                              |
| Current tab URL and title, page origin, YouTube video ID, document ID                                                       | Showing feature availability and the target tab, restoring an immersive translation session on the same origin, fetching YouTube captions | URL, title, and video ID are used only while running. The active session's tab ID, origin, and document ID are kept in `browser.storage.session`                                                               | Only the video ID and caption requests are sent to YouTube                                                                                                                           |
| Service request times, counts, estimated tokens, cooldowns; credential creation and last-success times; vault last-use time | Limiting excessive requests, recovering from failures, showing key lifecycle, auto-locking the vault                                      | Request-limit data and the vault last-use time are kept in `browser.storage.session`; credential metadata in extension-only IndexedDB                                                                          | Not sent anywhere                                                                                                                                                                    |

Settings change notifications record only the changed key names and a notification ID in `browser.storage.session`. They contain no setting values or API keys and are not sent anywhere.

Pages and selected text can contain personal information or private communications. justrans does not classify or profile this content, but when the user runs a feature, that content can be processed as described above.

## Data sent to external services

Translations go only to the service the user chose for selection translation and for immersive translation, and summaries only to the service chosen for summaries. justrans never sends a request to a service the user did not choose instead. If the chosen service is not available in the current browser, justrans shows an error without sending the request.

- **Google Translate (no API key, default):** The text to translate and the source and target languages are sent to `https://translate.googleapis.com`.
- **Chrome Translator (Chrome only):** The text to translate and the page's declared language are processed on the device by Chrome's built-in translation model and language detection, and are not sent anywhere. Chrome downloads a translation model when the user requests it in the settings page.
- **LLM services:** The translation or summary source text, surrounding context, user prompt, model information, and API key are sent to the endpoints below.

| Service                    | Endpoint                                    | Privacy policy that applies to received data                                                                                    |
| -------------------------- | ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Google Gemini              | `https://generativelanguage.googleapis.com` | [Google Privacy Policy](https://policies.google.com/privacy)                                                                    |
| OpenAI                     | `https://api.openai.com`                    | [OpenAI Privacy Policy](https://openai.com/policies/privacy-policy/)                                                            |
| Anthropic                  | `https://api.anthropic.com`                 | [Anthropic Privacy Policy](https://www.anthropic.com/legal/privacy)                                                             |
| Groq                       | `https://api.groq.com`                      | [Groq Privacy Policy](https://groq.com/privacy-policy)                                                                          |
| OpenRouter                 | `https://openrouter.ai`                     | [OpenRouter Privacy Policy](https://openrouter.ai/privacy). Forwards requests to the provider of the model the user chose       |
| Vercel AI Gateway          | `https://ai-gateway.vercel.sh`              | [Vercel Privacy Notice](https://vercel.com/legal/privacy-notice). Forwards requests to the provider of the model the user chose |
| Custom (OpenAI-compatible) | Base URL set by the user                    | Privacy policy of that endpoint's operator                                                                                      |

OpenRouter and Vercel AI Gateway relay requests to other model providers, so both the relay's policy and the final model provider's policy apply.

- **YouTube:** When reading captions, the video ID and caption requests are sent to the current YouTube origin and to the caption URL provided by YouTube. These requests do not include YouTube login cookies. Extracted captions are sent to the LLM service chosen for summaries only when the user runs a caption summary.

### Prefetch range of immersive translation

To avoid waiting while the user scrolls, immersive translation also translates paragraphs that are not yet visible. With Google Translate, it sends up to 8 screens of source text above and below the current screen in advance. LLM services prefetch 0.5 to 1 screen above and below depending on scroll speed, and Chrome Translator prefetches 1 screen. Prefetch runs only in tabs where the user started immersive translation.

Remote service traffic must use HTTPS. HTTP is allowed only for `localhost`, `127.0.0.1`, and `[::1]` endpoints on the same device. Retention and processing of data received by each service follow the policies in the table above. justrans does not control any service's training, logging, or retention policies. Over HTTPS, a service can observe network information such as IP address, request time, and user agent.

## Retention and deletion

- Translation source text and results are kept in the translation cache in `browser.storage.session` so the same content is not sent to a service again. Each entry is removed after 1 hour or when the cache exceeds 5,000 entries or about 4 MiB, and the whole cache is removed when the browser restarts or the extension reloads. Chrome does not write this area to disk.
- Results received through prefetch stay in the same cache even if the user never viewed them. Content translated in a private (incognito) window also stays in the same extension session for that period after the window is closed.
- Summary input and output are not written to browser storage. Translation and summary source text and results are never written to history or to persistent storage such as `storage.local`, `storage.sync`, or IndexedDB.
- Session-stored API keys, an unlocked vault, active immersive translation sessions, and request-limit data are removed when the browser restarts or the extension reloads.
- Persistently stored API keys can be removed with `Delete key` in the settings page. This does not delete data held by the service or revoke the key in the service's console. If the user forgets the vault password, resetting the vault deletes all persistently stored keys.
- Prompts and general settings can be deleted by changing them or removing the extension.
- Requests to view or delete data a service has already received must be made to that service or to the operator of the endpoint the user configured.

## Sharing and limited use

justrans does not sell user data or use it for advertising, profiling, data brokers, credit assessment, or lending. It does not send user data to the developer's servers and does not share it with anyone other than the services needed to provide the features above. The operator does not receive user content or run any process for people to read it. Access to data received by a service is governed by that service's privacy policy.

The use of information received from Google APIs will adhere to the Chrome Web Store User Data Policy, including the Limited Use requirements.

## Security

justrans requests host permission for all HTTP and HTTPS sites (`http://*/*`, `https://*/*`) at install time so it can translate and summarize the regular web pages the user chooses and communicate with Google Translate, YouTube, and the service endpoints the user configures. The content runtime loads on regular HTTP and HTTPS pages, but it processes page content and sends data only after the user runs a translation or summary feature. The content runtime loaded into web pages does not read the extension's settings storage or `browser.storage.session` directly (Chrome restricts the session area to extension pages, and Firefox does not expose the session area to content scripts).

Service endpoints are shown in the settings page, and each API key is bound to its configured service, origin, and base path so that sending it to any other endpoint is refused. Without a password, persistent-storage encryption does not protect against theft of the local browser profile. Even with a password, it does not protect against code with the same extension privileges or DevTools access while the vault is unlocked. The vault locks automatically after the configured idle time (30 minutes by default).

## Firefox data collection disclosure and API credential permission

The Firefox build declares website content processing (`websiteContent`) as required data collection and authentication information (`authenticationInfo`) as optional data collection at install time. Entering or saving an API key does not send any request to a service. API keys authenticate model listing, translation, and summary requests to the configured service. In Firefox, the existing API access button requests the browser's built-in permission to send authentication information. If the user declines the permission or revokes it in Firefox settings, later requests that use an API key are blocked. Requests without a key do not require the authentication permission.

## Changes and contact

When the data processed, where it is sent, or how it is stored changes, the effective date of this document and its [Korean version](./PRIVACY.ko.md), and the Chrome Web Store and Firefox Add-ons disclosures, are updated before the feature ships. Questions and deletion support requests can be filed in the [project issue tracker](https://github.com/pravusid/justrans/issues).
