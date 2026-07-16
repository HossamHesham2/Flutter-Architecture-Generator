# FlutterArchitect

PowerShell scaffolding toolkit that generates a **Clean Architecture** Flutter project skeleton in seconds — base app structure plus repeatable feature modules — so you spend time writing business logic instead of boilerplate.

Two scripts, two jobs:

| Script | Purpose |
|---|---|
| `genBaseFolders.ps1` | One-time setup: creates the app-level `core/`, `app/`, routing, theming, and installs the standard dependency stack. |
| `genFeature.ps1` | Repeatable: generates a full `data / domain / presentation` module for a single feature (e.g. `auth`, `profile`, `notes`). |

---

## ✨ What it generates

### `genBaseFolders.ps1`

```
lib
├── app
│   ├── my_app.dart          MaterialApp.router + ScreenUtilInit
│   └── observers
│       └── app_observer.dart   BlocObserver for global logging
├── core
│   ├── constants
│   ├── di
│   ├── errors               Failure / Exception base classes
│   ├── extensions
│   ├── helpers
│   │   ├── prefs_helper.dart    SharedPreferences wrapper
│   │   └── validators.dart      Email / password / name / amount validators
│   ├── router
│   │   ├── app_router.dart      GoRouter config
│   │   └── route_names.dart
│   ├── theme
│   │   ├── app_colors.dart
│   │   └── app_theme.dart       Light / dark ThemeData
│   ├── utils
│   │   └── logger.dart
│   └── widgets
│       └── custom_text_field.dart
└── main.dart

assets/
├── images/
└── svgs/
```

It also:
- Reads the package name from `pubspec.yaml` and fixes it into generated imports automatically.
- Overwrites the default `test/widget_test.dart` so it points at `MyApp` instead of the counter-app template Flutter ships with.
- Adds the toolkit's standard dependency stack via `flutter pub add` (always the latest compatible version — nothing is hard-pinned): `flutter_bloc`, `equatable`, `dartz`, `get_it`, `injectable`, `dio`, `shared_preferences`, `flutter_screenutil`, `go_router`, `firebase_core`, `firebase_auth`, `cloud_firestore`, plus `build_runner`, `injectable_generator`, `flutter_lints` as dev dependencies.
- Registers `assets/images/` and `assets/svgs/` under `flutter: assets:` in `pubspec.yaml`.
- Runs `flutter pub get` at the end.

### `genFeature.ps1`

```
lib/features/<feature_name>
├── data
│   ├── datasources/{remote,local}
│   ├── models/<feature>_model.dart      extends the domain entity
│   └── repositories/<feature>_repository_impl.dart
├── domain
│   ├── entities/<feature>_entity.dart
│   ├── repositories/<feature>_repository.dart
│   └── usecases/get_<feature>_usecase.dart
└── presentation
    ├── bloc/<feature>_bloc.dart, _event.dart, _state.dart
    ├── pages/<feature>_page.dart        wired to the Bloc
    └── widgets/
```

Class names are auto-derived from the feature name (`payment_history` → `PaymentHistory`), so every generated class, event, and state is consistently named.

---

## ⚙️ Requirements

- Windows PowerShell or PowerShell 7+ (`pwsh`) on macOS/Linux
- Flutter SDK on your `PATH`
- Run from the **root of a Flutter project** (a folder containing `pubspec.yaml`)

## 🚀 Usage

```powershell
# 1. One-time: scaffold the base architecture
.\genBaseFolders.ps1

# 2. Generate a feature module (repeat per feature)
.\genFeature.ps1 -FeatureName auth
.\genFeature.ps1 -FeatureName profile
```

> Tip: put both scripts in a `tools/` folder in your Flutter template repo, or on your `PATH`, so you can run them from any new project.

---

## 🔧 Enhancements in this version

The original scripts had a few gaps that were fixed here:

1. **Routing modernized to `go_router`.** The base generator used to build a manual `Navigator`/`onGenerateRoute` setup; it now ships an `AppRouter` (`GoRouter`) wired through `MaterialApp.router`, which scales far better once you add nested/shell routes per feature.
2. **Broken `$PackageName` interpolation fixed.** Several generated files used single-quoted PowerShell here-strings (`@' ... '@`), which don't expand variables — so `$PackageName` was being written into `.dart` files as literal text instead of the real package name. Files that need it now use double-quoted here-strings (`@" ... "@`).
3. **Missing import fixed.** `main.dart` called `PrefsHelper.instance.init()` without importing it — added.
4. **`genFeature.ps1` no longer silently overwrites your code.** Previously, re-running it on an existing feature would blindly regenerate every file — wiping out anything you'd written in the bloc, page, or repository impl. It now checks each file with `Test-Path` first and skips (with a clear log line) anything that already exists, matching how the folder-creation loop already behaved.
5. **Filled in the empty folders.** `domain/usecases`, `data/models`, and `presentation/pages` were created but never populated. The generator now drops a starter use case, a model that extends the entity (with `fromJson`/`toJson` stubs), and a page pre-wired with `BlocProvider` + `BlocBuilder`.
6. **`genFeature.ps1` now guards on `pubspec.yaml`** the same way the base script does, so it fails fast with a clear message instead of creating a stray `lib/features` folder outside a Flutter project.

## 🗺️ Suggested next steps (not yet implemented)

- Auto-register each feature's dependencies (repository, use cases, bloc) into `core/di` with `get_it` + `injectable` instead of leaving DI wiring manual.
- Add a `--riverpod` flag as an alternative to BLoC for teams that prefer it.
- Generate a matching `test/features/<feature>/` skeleton (unit tests for the bloc and repository) alongside the feature.
- Add the new feature's route automatically into `app_router.dart` and `route_names.dart` instead of leaving that as a manual step.
- Package both scripts as a single PowerShell module (`Import-Module FlutterArchitect`) with `New-FlutterProject` / `New-FlutterFeature` cmdlets instead of two standalone `.ps1` files.

## 📄 License

Add a license of your choice (MIT is a common pick for tooling like this) before publishing.

---

## 👤 Author

Made by **Hossam** — Flutter Developer.
Connect on [LinkedIn](https://www.linkedin.com/in/hossam-hesham-b5283b254) if you'd like to talk Flutter architecture, BLoC, or Clean Architecture patterns.
