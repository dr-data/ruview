# Aibes rebranding plan

Status: **phased migration, draft PR #1, not approved for release**  
Repository: [dr-data/ruview](https://github.com/dr-data/ruview)  
Working branch: `branding/aibes`  
Upstream project: [ruvnet/RuView](https://github.com/ruvnet/RuView)  
Audit: 11 November 2026

## Decision and scope

**Aibes** is the public product name for this downstream adaptation. This phase must retain working WiFi CSI capture, training, sensing, dashboard, device provisioning, MQTT/HomeKit/Matter integrations, and the documented upstream provenance.

Treat each occurrence of `RuView`, `ruview`, and `RUVIEW` as one of four classes before changing it:

| Class | Examples | Rule |
| --- | --- | --- |
| Display brand | Window titles, app labels, dashboard headings, graphic titles, screenshots, printed welcome text | Change to Aibes |
| Generated output | SVG cards and manifest, PWA icons, mobile splash and icon files, programmatic brand templates | Change the *generator or source* first, then regenerate and verify outputs |
| Compatibility identity | Rust crate names and paths, npm and PyPI names, Python imports, JSON keys, CLI commands, environment variables, Tauri/bundle IDs, OTA fields, mDNS/DNS-SD, MQTT identities | Preserve initially; migrate with explicit aliases, versioning and integration tests |
| History and origin | Upstream GitHub URLs, copyright, LICENSE, NOTICE, vendored third-party provenance, ADRs, past benchmarks, original authorship | Preserve original attribution and original proper names where historically accurate |

**Do not** run a repository-wide, case-insensitive text replacement. Success means no *unclassified visible brand* remains, not that the string `ruview` disappears from functional or historical artifacts.

## Current baseline

The branch began from `main` at `dea1cc23e1efb3d409fb00a99bf68fed9955a24b`.

As audited before adding this document, draft [PR #1](https://github.com/dr-data/ruview/pull/1) contained **9 commits and 48 modified or added files**. The branch tree had **3,988 tracked blobs**, including **485 paths** containing `ruview` or `aibes` in their names. The path count alone illustrates why file and crate renames must be handled independently of visible branding.

The draft has already modified primary README content, a new Aibes hero, some current SVGs, web and mobile display labels, nvsim dashboard labels, some documentation and integration display strings. The main branch remains unchanged. No passing build or CI results were observed at the audited PR head. **Existing commits are not a proof of compatibility.**

### Gaps and corrections found during the audit

1. `scripts/render-readme-visuals.py` still emits `RuView` or `RUVIEW` in generated SVGs and `assets/readme/manifest.json`. Direct edits to the generated assets will be overwritten. Fix the generator and check that a second generation is idempotent.
2. Desktop UI still displays RuView in `v2/crates/wifi-densepose-desktop/ui/src/App.tsx`, `src/components/Sidebar.tsx`, `ui/index.html` and `tauri.conf.json` product name/window title.
3. The existing README says “The Aibes-specific metaharness we created is published as `@ruvnet/ruview`.” This incorrectly suggests the downstream project published the upstream npm package. Correct it to an explicit upstream dependency attribution.
4. PWA files under `ui/icons` and native mobile assets remain to be audited for embedded RuView artwork. The prior change to the icon generator does not itself recreate existing PNG files.
5. The service worker uses the cache identifier `ruview-v2`. This is a compatibility/cache key, not merely a visible brand. If stale assets persist after branding, introduce a new cache version and test upgrade behavior.
6. The Tauri identifier `net.ruv.ruview`, Expo `com.ruvnet.wifidensepose` identifiers, `ruview` Python module, `@ruvnet/ruview` npm package and other published names are **not** display strings.
7. Device discovery uses `_ruview._tcp.local.` and `ruview.installation.v1` in `v2/crates/wifi-densepose-sensing-server/src/discovery.rs`, and firmware hostnames start with `ruview-node-` in `firmware/esp32-csi-node/main/node_mdns.c`. Changing these without a compatibility period can make clients and nodes undiscoverable.
8. `LICENSE` contains the MIT licence, while the root `NOTICE` describes “MIT OR Apache-2.0” and also covers separately licensed third-party content. Review that inconsistency without erasing original copyright or provenance.
9. Several capabilities and accuracy claims in the source README are inherited from upstream. Rebranding text is not validation that a downstream deployment has achieved those capabilities. Keep simulation and experimental limitations visible.

## Part 0: audit and migration policy

**Goal:** classify names, preserve attribution, establish a test baseline.

Changes: this plan, a machine-readable exception list if needed, and documented protected strings. Do not change business logic yet.

Gate: `git diff --check`; review all current 48 file changes and confirm original author attribution, licensing, and compatibility tokens are preserved. Establish a baseline build or document test blockers.

## Part 1: brand source of truth and graphics

**Goal:** make visual rebranding reproducible.

Scope: `scripts/render-readme-visuals.py`, `assets/readme/manifest.json`, the SVGs actually embedded in the README, `assets/aibes-hero.svg`, icon generation sources, any remaining active animated artwork.

Order: update script constants; regenerate only managed assets; inspect SVG titles/text/alt metadata; verify links and motion-reduction semantics; ensure generation produces no further changes on a second run.

Gate: XML/SVG parsing passes, no visible RuView on current Aibes graphics, all README image paths resolve, generated files are stable. Never modify source assets and generated outputs in different phases without synchronization.

## Part 2: user-facing application surfaces

**Goal:** all active interfaces and application branding display Aibes.

Scope: static web UI `ui/`, nvsim dashboard `dashboard/`, desktop React/Tauri `v2/crates/wifi-densepose-desktop/`, Expo app `ui/mobile/`, companion LiDAR views, Homecore UI where the parent product name is shown.

Desktop: change Tauri `productName` and window `title`, React heading, sidebar and HTML title. **Keep the Tauri application identifier** until a planned application ID migration.

Mobile: reconcile `ui/mobile/app.config.ts` and `ui/mobile/app.json`, inspect physical icons and splash assets, retain existing bundle IDs to protect installation continuity.

Web/PWA: verify page titles, manifest, onboarding, screenshots, service-worker asset upgrades and cached clients. **Do not remove simulation/offline indicators.**

Gate: `cd dashboard && npm ci && npm run build && npm test`; `cd v2/crates/wifi-densepose-desktop/ui && npm ci && npm run build`; Expo configuration and web smoke test; browser screenshot review, including old-cache upgrade test. Run only commands appropriate to the available environment and report unrun checks.

## Part 3: runtime messages and device surfaces

**Goal:** Aibes in visible CLI, backend logging, pairing names and human-readable sensor labels.

Scope: installer and doctor banners, `harness/ruview/src/` messages, sensing server help and page titles, HomeKit accessory display name, firmware console/onboarding text, printed device name where supported.

Gate: test server startup and health endpoint, provisioning, mDNS discovery, HTTP/UDP CSI ingestion, MQTT/Home Assistant discovery, HomeKit pairing, Matter paths and the selected ESP32 target. Preserving exact existing wire IDs is the default.

**Do not silently rename:** `RUVIEW_API_TOKEN`, `RUVIEW_UDP_ALLOW`, `_ruview._tcp.local.`, `ruview.installation.v1`, `ruview-node-`, internal JSON schemas or other integration identifiers.

## Part 4: documentation, installers and claims

**Goal:** consistent Aibes documentation with technically correct commands.

Scope: `README.md`, current guides in `docs/`, `install.sh`, `deploy.sh`, Docker documentation, sample configuration and user-visible examples.

Rules: explain that Aibes builds on RuView, retain required notices and the original upstream URLs, distinguish legacy published commands from Aibes-branded names that do not yet exist, keep the Raspberry Pi deployment path clear, and preserve original research history and accuracy caveats.

Gate: Markdown link checks, runnable verified command examples, no misleading claim that Aibes owns an upstream npm/PyPI package or has reproduced its model performance.

## Part 5: public package and API migration (separate high-risk change)

**Goal:** allow a genuinely independent Aibes distribution, only if desired.

Inventory before deciding:
* npm: `@ruvnet/ruview`, `@ruvnet/ruview-kernel`, unscoped `ruview`.
* PyPI/Python: `ruview`, `wifi-densepose`, `from ruview import ...`.
* Rust: `ruview-*` crates, `wifi-densepose-*` crates and numerous path dependencies.
* Configuration/protocol: `RUVIEW_*` env names, mDNS service/schema, MQTT identifiers, UI storage keys.
* Native app: Tauri identifier `net.ruv.ruview`, Expo and Android/iOS package identities.

Proposed strategy: choose Aibes namespace availability before publication; publish Aibes-named packages only with correctly updated import paths; keep old identifiers as compatibility aliases for a documented transition period; version breaking wire/schema changes; test clients against both old and new discovery and configuration names.

Gate: green package builds/tests, verified installation from a clean environment, dependency-lock updates, device and client compatibility matrix, migration guide and rollback procedure. Never infer namespace ownership or publish under `@aibes` without confirming access.

## Part 6: repository identity and release

**Goal:** decide whether to rename the GitHub repository itself.

The present repo URL is `dr-data/ruview`. Renaming to a new slug such as `dr-data/aibes` is **not** required for the current visual branding PR and should be a separate controlled decision. Update badges, GitHub Pages base paths, release automation, links, remotes and documentation only after dependency analysis.

Gate: tests passing, copyright/NOTICE review, real hardware smoke test, links and package availability checked, PR reviewed, migration communication prepared. Keep a rollback ref.

## Merge and QA checklist

1. Every modified component must have its own reviewable commit or a separate PR, with its exact scope in the commit description.
2. Keep a protected compatibility identifier inventory and compare before/after values.
3. Check static views for Aibes, including desktop, mobile, generated SVGs, offline cache, splash, icons, onboarding, logs and CLI.
4. Avoid claims of human counting, pose or vital-sign accuracy unless supported by actual measured evidence.
5. Run the relevant Rust, Python, Node, PWA, mobile and hardware tests for each component. Record tests that cannot run.
6. Do not merge into `main` until all acceptance gates for the intended release scope pass.

**Recommended next commit:** repair the image generator and misleading README attribution, validate their outputs and only then start desktop UI changes.
