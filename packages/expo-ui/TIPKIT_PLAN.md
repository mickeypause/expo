# TipKit Integration Plan for Expo UI

## Goal

Add first-class TipKit support to `@expo/ui` (SwiftUI side) so apps can highlight features using native iOS TipKit behavior from React Native.

## Why

- TipKit is Apple's native mechanism for onboarding and feature discovery.
- Expo UI already maps SwiftUI primitives and modifiers; TipKit fits that model.
- No existing TipKit support is present in this repo today.

## Scope (MVP)

1. Add basic TipKit capability for iOS 17+ only.
2. Support two primary surfaces:
   - `TipView`-style inline tip rendering.
   - `popoverTip`-style attachment to existing SwiftUI Expo UI components.
3. Add module-level utility methods for:
   - configuring TipKit once
   - resetting tip state for development/testing
4. Provide docs and an example screen in native-component-list.

## Non-goals (for MVP)

- Full TipKit parity (complex rules/events/actions/donations API).
- Android/web equivalents.
- Opinionated fallback UI for iOS < 17 (MVP should no-op cleanly).

## Constraints

- `expo-ui` currently supports iOS 15.1+, while TipKit requires iOS 17+.
- Must use strict availability guards (`@available(iOS 17.0, *)`).
- API must stay consistent with existing Expo UI patterns:
  - component wrappers in TypeScript
  - modifier-driven customization
  - event dispatch via existing modifier event plumbing

## Proposed Architecture

### Native iOS

- Add TipKit-backed implementation in `packages/expo-ui/ios/`.
- Register new views/functions in `ExpoUIModule.swift`.
- Add TipKit modifier(s) through `ViewModifierRegistry` where appropriate.
- Ensure behavior is no-op on unsupported OS versions.

### TypeScript API

- Export new APIs from `@expo/ui/swift-ui` and/or `@expo/ui/swift-ui/modifiers`.
- Keep naming aligned with SwiftUI/TipKit conventions.
- Provide typed props and clear platform/version notes.

### Docs + Samples

- Add docs pages under:
  - `docs/pages/versions/unversioned/sdk/ui/swift-ui/`
  - versioned docs as needed
- Add native-component-list iOS screen for manual validation and demos.

## Risks and Problems to Solve

1. **Type modeling mismatch**
   - TipKit is protocol/type driven; Expo UI bridge is JSON config driven.
2. **Lifecycle/config**
   - TipKit expects centralized configuration once at startup.
3. **State persistence semantics**
   - Tip IDs/rules must remain stable across updates.
4. **Anchor behavior**
   - `popoverTip` attachment must behave correctly across Host boundaries.
5. **Testing determinism**
   - Need reset/debug hooks to avoid flaky behavior in development/QA.
6. **Backward compatibility**
   - iOS < 17 must degrade safely with explicit documentation.

## Milestones

### Milestone 1: API design

- Finalize MVP public API shape.
- Decide no-op/error behavior on unsupported platforms.
- Document naming and compatibility guarantees.

### Milestone 2: Native + TS implementation

- Implement iOS TipKit plumbing.
- Add exports/types in TS.
- Wire in module helper functions (configure/reset).

### Milestone 3: Validation

- Add native-component-list sample screen.
- Verify behavior on iOS 17+ and iOS 15/16 no-op behavior.

### Milestone 4: Documentation

- Add usage docs, constraints, and known limitations.
- Include testing/debug recommendations.

## Open Questions (to resolve before implementation)

1. Should `Tips.configure` be called automatically or explicitly by app code?
2. Should unsupported platforms hard-warn in dev, or silently no-op?
3. Do we ship only MVP surfaces first, then add rules/events/actions later?
4. Should reset/debug APIs be exposed publicly or dev-only?

## Initial Task Checklist

- [ ] Finalize API proposal and naming
- [ ] Implement native TipKit bridge (iOS 17+ guards)
- [ ] Add TS wrapper exports and typings
- [ ] Add configure/reset module methods
- [ ] Add native-component-list iOS demo screen
- [ ] Add docs (unversioned + current version)
- [ ] Add changelog entry
