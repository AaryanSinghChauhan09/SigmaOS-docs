# Components

This is the full registry of SigmaOS repositories. The system is organised into four
tiers (see [Architecture](Architecture.md)); every repo belongs to exactly one tier and
depends only on repos in tiers below it.

The registry covers **161+ repositories**: 2 kernel, 3 core subsystems, 11 system/desktop
repos, and 146 userland component repos. Each row lists the repo name, a short
description, its tier, its license, its current build status, and an upstream reference
where applicable.

> Status legend: **scaffold** = repo scaffolded, compiles cleanly under
> `cargo check --lib`, has tests and CI in place, awaiting full implementation wiring.

---

## Tier 0 — Kernel (2)

The foundation. Mechanism only; runs in kernel space.

| Name | Description | Tier | License | Status | Upstream reference |
|------|-------------|------|---------|--------|--------------------|
| [SigmaOS-kernel](https://github.com/AaryanSinghChauhan09/SigmaOS-kernel) | Microkernel core: scheduler, memory management (buddy + paging), syscalls, interrupts, capsicum, cgroups, boot-to-userspace | Tier 0 | MIT | scaffold | SigmaOS kernel |
| [SigmaOS-bootloader](https://github.com/AaryanSinghChauhan09/SigmaOS-bootloader) | Boot integration and early bring-up configuration | Tier 0 | MIT | scaffold | SigmaOS kernel |

---

## Tier 1 — Filesystem / Network / Security (3)

Core subsystems sitting on the kernel, exposing mechanism to higher tiers.

| Name | Description | Tier | License | Status | Upstream reference |
|------|-------------|------|---------|--------|--------------------|
| [SigmaOS-filesystem](https://github.com/AaryanSinghChauhan09/SigmaOS-filesystem) | Virtual filesystem layer; uniform interface across concrete filesystem implementations | Tier 1 | MIT | scaffold | SigmaOS-kernel |
| [SigmaOS-network](https://github.com/AaryanSinghChauhan09/SigmaOS-network) | Networking stack: socket mechanism, packet routing, TLS context | Tier 1 | MIT | scaffold | SigmaOS-kernel |
| [SigmaOS-security](https://github.com/AaryanSinghChauhan09/SigmaOS-security) | Privilege separation, capability framework, LSM hooks, sandbox infrastructure | Tier 1 | MIT | scaffold | SigmaOS-kernel |

---

## Tier 2 — System / Desktop / Packages (11)

Orchestration, desktop stack, and packaging. Where policy lives.

| Name | Description | Tier | License | Status | Upstream reference |
|------|-------------|------|---------|--------|--------------------|
| [SigmaOS-system](https://github.com/AaryanSinghChauhan09/SigmaOS-system) | Init system and service orchestration; defines and validates the boot chain | Tier 2 | MIT | scaffold | filesystem, security |
| [SigmaOS-desktop](https://github.com/AaryanSinghChauhan09/SigmaOS-desktop) | Wayland compositor and desktop umbrella | Tier 2 | MIT | scaffold | system, filesystem |
| [SigmaOS-packages](https://github.com/AaryanSinghChauhan09/SigmaOS-packages) | Package manager core: dependency resolution, signature verification, DBus API | Tier 2 | MIT | scaffold | system, filesystem |
| [SigmaOS-compositor](https://github.com/AaryanSinghChauhan09/SigmaOS-compositor) | Compositor surface lifecycle management | Tier 2 | MIT | scaffold | desktop |
| [SigmaOS-window-manager](https://github.com/AaryanSinghChauhan09/SigmaOS-window-manager) | Tiling layout and workspace management | Tier 2 | MIT | scaffold | compositor |
| [SigmaOS-terminal](https://github.com/AaryanSinghChauhan09/SigmaOS-terminal) | Terminal emulator with PTY lifecycle and VT parsing | Tier 2 | MIT | scaffold | desktop |
| [SigmaOS-file-manager](https://github.com/AaryanSinghChauhan09/SigmaOS-file-manager) | File manager with directory listing and search | Tier 2 | MIT | scaffold | filesystem |
| [SigmaOS-text-editor](https://github.com/AaryanSinghChauhan09/SigmaOS-text-editor) | Text editor with rope backend and syntax highlighting | Tier 2 | MIT | scaffold | desktop |
| [SigmaOS-theme-engine](https://github.com/AaryanSinghChauhan09/SigmaOS-theme-engine) | Theme engine with pluggable renderers and color synthesis | Tier 2 | MIT | scaffold | desktop |
| [SigmaOS-settings](https://github.com/AaryanSinghChauhan09/SigmaOS-settings) | Settings panel registry with persistence | Tier 2 | MIT | scaffold | system, desktop |
| [SigmaOS-app-framework](https://github.com/AaryanSinghChauhan09/SigmaOS-app-framework) | Widget framework with accessibility tree | Tier 2 | MIT | scaffold | theme-engine, desktop |

---

## Tier 3 — Userland Components (146)

All 146 component repos scaffolded in Phase 4. Each is an independently compilable Rust
crate with zero external dependencies in its scaffold (YAGNI applied to dependencies),
its own CI, and a documented `COMPONENTS-entry.toml`. They are grouped below by category.

### System Services (19)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| Sigma | Core meta-package and system integration for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-backup-tool | System backup and restore utility for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-bluetooth | Bluetooth system integration and applet for SigmaOS desktop | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-bootloader | System bootloader integration and configuration for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-configurator | System configurator and first-run setup wizard for SigmaOS, guiding users through initial system configuration. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-control-center | Unified system control center for SigmaOS, aggregating all system settings panels in a single interface. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-daemon-settings | Settings daemon for desktop services and preferences | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-drivers | Hardware driver management tool for SigmaOS, detecting, installing, and updating proprietary and third-party drivers. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-installer | Graphical system installer application for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-live-session | Live session management for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-mirror | Repository mirror management tool for automating package repository synchronisation | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-plymouth | Boot splash screen for SigmaOS with animated startup graphics and theme management | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-power-manager | Power management and battery tool for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-session-manager | Desktop session management for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-setup | Initial system setup wizard for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-system | Core system utilities and configuration for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-system-locale | System locale and language settings for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-systemd | Systemd integration and management tools for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-xorg-server | Xorg display server integration for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Desktop/UI (31)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-audio-tuner | System audio tuning and equalizer tool for SigmaOS desktop | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-calculator | Desktop calculator application for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-chromium | Chromium browser integration and patches for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-desktop-configuration | Desktop environment configuration tool for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-desktop-environment | Full desktop environment stack for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-desktop-library | Shared library for SigmaOS desktop environment components | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-desktop-portal | XDG desktop portal implementation for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-display-manager | Graphical login and display manager for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-file-writer | Text file editor and writer application for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-folder-color-switcher | Folder icon color customizer for SigmaOS file manager | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-greeter | Graphical login greeter for SigmaOS display manager | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-GTK | GTK toolkit integration and patches for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-GTK-plus | GTK+ application suite integration for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-menus | Desktop application menus system implementing the freedesktop.org menu specification | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-notification | Desktop notification system implementing the freedesktop.org notification specification | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-panel | Desktop taskbar and panel for SigmaOS with applet support, workspace switcher, and system tray | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-plugin-marketplace | Plugin and extension marketplace for SigmaOS with curated catalogue and one-click installation | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-plugin-registry | Plugin registry and discovery service for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-quickshell | Keyboard-driven application launcher and shell with fuzzy search for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-screen-recorder | Desktop screen recording tool for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-screen-saver | Desktop screensaver and lock screen for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-sticky-notes | Desktop sticky notes application for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-term | Terminal emulator with multiple backend support for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-color | Color theme definitions and palettes for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-cursor | Cursor theme collection for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-wallpaper-engine | Dynamic wallpaper and background engine for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-webapp-manager | Web application manager and PWA launcher for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-webcam-recorder | Webcam capture and recording tool for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-welcome-screen | First-run welcome screen and onboarding for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-xapp-thumbnailers | File thumbnailer plugins for SigmaOS xapp | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-xreader | Document reader application for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Themes (10)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-theme-desktop-environment | Desktop environment themes for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-gtk3 | GTK3 theme collection for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-icon | Icon theme collection for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-marketplace | Theme discovery and download marketplace for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-mdm | Display manager theme collection for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-registry | Central theme registry and metadata store for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-support | Theme compatibility library for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-sync | Cross-application theme synchronization tool for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theme-y | Y theme series for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-theming-native | Native theming engine integration for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |

### Utilities (28)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-asdcontrol-git | Component for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-automate | Desktop automation and macro recording tool for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-bluetooth-tool | Bluetooth management GUI for pairing and device control | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-clock-global | World clock and timezone management tool for SigmaOS, showing times across multiple configured timezones. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-daemon-apt | Component for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-file-manager-system | Component for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-fish | Fish shell configuration and plugins for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-screen-shot | Screenshot capture tool for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-simple-scan | Document scanner application for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-software-manager | Software center and app store for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-stick | Bootable USB drive creator for SigmaOS. Flash ISO images to USB drives safely and reliably. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-system-admin | Component for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-terminal-text-effects | Terminal text animation effects for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-thingy | SigmaOS userland component | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-timeshift | SigmaOS userland component | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-tray-icon | SigmaOS userland component | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-troubleshooting-tool | SigmaOS userland component | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-try | Component for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-try-windows | Windows environment trial launcher for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-tte-go | Terminal text effects engine in Go for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-upgrade-legacy | Legacy system upgrade support tool for SigmaOS. Enables upgrade paths from older or unsupported SigmaOS versions, handling compatibility and migration tasks. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-upload | File upload utility for SigmaOS. Upload files to remote servers via FTP, SFTP, or HTTP with progress tracking. Inspired by mintupload. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-video-cut | Component for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-we-can-fix-everything | Community issue tracking and fix coordination for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-window-management-library | Window management shared library for SigmaOS. Provides core window management APIs, compositing support, and workspace handling. Inspired by muffin (Cinnamon's window manager). | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-xapp | Cross-desktop application library for SigmaOS. Provides shared widgets, utilities, and APIs for building SigmaOS applications that work across desktop environments. Inspired by xapp. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-xapp-python | Python bindings for the SigmaOS xapp library. Enables Python applications to use SigmaOS xapp widgets and APIs. Inspired by python3-xapp. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-zsh | Zsh shell configuration and plugins for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Networking (5)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-chatroom | Community chat client for SigmaOS project communication | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-LAN-fileshare | Local network file sharing for SigmaOS with drag-and-drop peer-to-peer transfer | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-network-discovery | Network device discovery tool scanning local networks via mDNS and UPnP | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-remote-desktop | Remote desktop client and server for SigmaOS supporting VNC and RDP protocols | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-vpn-pia | VPN client integration for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Security (3)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-domain-blocker | Network domain blocking and filtering tool for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-security | Security framework and hardening tools for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-sign | Digital document signing tool for SigmaOS using GPG and X.509 certificates | Tier 3 | MIT | scaffold | SigmaOS component |

### Package Management (7)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-apturl | URL handler that allows installing packages from apt:// URIs, integrating with the system package manager. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-packages-kit | Package management toolkit for SigmaOS providing a high-level DBus API across multiple package manager backends | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-packages-tools | Package installation tools for SigmaOS with GUI and CLI helpers, dependency resolution, and signature verification | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-update | System package update manager for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-upgrade | System version upgrade tool for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-upgrade-info | Upgrade release information provider for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-wine-installer | Windows application compatibility installer for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Developer Tools (5)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-dev-tools | Developer tools and utilities for SigmaOS development | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-github-action | Reusable GitHub Actions workflows for SigmaOS repositories | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-herdr-runtime | Runtime agent and live coding environment for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-nvim | Neovim configuration and plugin suite for SigmaOS with LSP and SigmaOS-themed colour schemes | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-vim | Vim and editor integration for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Documentation (9)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-brand-logo | Brand assets, logos, and visual identity for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-forum-themes | Community forum theme assets for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-guide-developer | Developer contribution guide for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-guide-docs | Documentation portal and guide index for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-guide-installation | Installation guide documentation for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-guide-translations | Translation guide and locale files for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-guide-troubleshooting | Troubleshooting guide documentation for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-guide-user | End-user guide documentation for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-website | Project website and documentation site for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Media (4)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-photo-viewer | Image viewer for SigmaOS with common format support, basic editing tools, and slideshow capabilities | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-player | Media player application for SigmaOS with video and audio playback, playlist management, and subtitle handling | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-radio-stream | Internet radio streaming client for SigmaOS with station discovery and panel integration | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-reel | Screen recording and reel creation tool for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Graphics (2)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-draw | Vector drawing and screen annotation application for SigmaOS, supporting SVG output and presentation aids. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-mesa3D | Mesa 3D graphics library integration providing OpenGL, Vulkan, and OpenCL for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### File Management (3)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-actions | File manager context menu actions and extensions for the SigmaOS file manager, allowing custom right-click actions. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-renamer-bulk | Bulk file renaming tool for SigmaOS with regex patterns and sequential numbering | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-renamer-file | Single file renaming utility for SigmaOS with conflict detection and undo support | Tier 3 | MIT | scaffold | SigmaOS component |

### Libraries (3)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-common | Shared common libraries and utility functions used across SigmaOS components for consistent behaviour. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-libappindicator | Application indicator library for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-player-library | Media player playlist library for SigmaOS with playlist parsing, metadata extraction, and streaming URI resolution | Tier 3 | MIT | scaffold | SigmaOS component |

### Runtime (3)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-JS-interpreter | JavaScript runtime interpreter for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-mozjs | Mozilla SpiderMonkey JavaScript engine integration for SigmaOS shell extensions | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-nodejs-binary | Pre-built Node.js binary distribution with version management for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Panel Applets (3)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-indicator-sound | Sound indicator applet for the SigmaOS panel | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-notification-center-plugin | Notification center panel plugin aggregating system notifications into a history panel | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-ssh-port-forward-plugin | SSH port forwarding panel plugin for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Build Tools (1)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-ISO | ISO image builder and configuration tooling for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Hardware (1)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-NVIDIA | NVIDIA GPU driver integration and PRIME switching support for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Accessibility (1)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-accessibility | Accessibility tools and AT-SPI bridge for SigmaOS, enabling assistive technology support for all desktop components. | Tier 3 | MIT | scaffold | SigmaOS component |

### Configuration (1)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-dots-configuration | Dotfiles and configuration management system for SigmaOS, enabling reproducible desktop environment setups. | Tier 3 | MIT | scaffold | SigmaOS component |

### Compatibility (1)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-mac-run | macOS application compatibility layer providing runtime shims for SigmaOS | Tier 3 | MIT | scaffold | SigmaOS component |

### Productivity (1)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-office | Office productivity suite integration for SigmaOS with SigmaOS-native theming and desktop integration | Tier 3 | MIT | scaffold | SigmaOS component |

### Tools (5)

| Name | Description | Tier | License | Status | Upstream |
|------|-------------|------|---------|--------|----------|
| SigmaOS-slide-presenter | Presentation tool for SigmaOS enabling creation and delivery of slide-based presentations | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-spreadsheet | Spreadsheet application component for SigmaOS, offering cell-based data entry, formulas, and chart generation | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-state-of-components | Component health dashboard for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-stream | IPTV and media streaming client for SigmaOS. | Tier 3 | MIT | scaffold | SigmaOS component |
| SigmaOS-wiki-extra | Extended wiki content and additional documentation for SigmaOS. Provides supplementary articles, tutorials, and reference material for the SigmaOS wiki. | Tier 3 | MIT | scaffold | SigmaOS component |

---

## Summary

| Tier | Repos | Description |
|------|-------|-------------|
| Tier 0 — Kernel | 2 | Microkernel foundation |
| Tier 1 — FS/Net/Sec | 3 | Core subsystems |
| Tier 2 — System/Desktop/Packages | 11 | Orchestration, desktop, packaging |
| Tier 3 — Userland | 146 | Applications and end-user components |
| **Total** | **162** | |

The source of truth for component metadata is `COMPONENTS.toml` (1,635 classified
components) and each repo's `COMPONENTS-entry.toml`. The dependency graph is a validated
DAG (zero cycles), enforced nightly by CI.
