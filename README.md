# 🏗️ FlutterArchitect

**Stop writing Clean Architecture boilerplate by hand.**
FlutterArchitect is a set of PowerShell scripts that scaffolds a full **Flutter Clean Architecture** project — folder structure, base files, dependencies, and feature modules — in seconds.

Built out of the repetitive pain of setting up `data / domain / presentation` layers for every new Flutter project and every new feature. Run one command, get a production-ready skeleton, start writing actual business logic.

---

## ✨ What it does

FlutterArchitect ships two scripts:

### 1. `genBaseFolders.ps1` — Project Bootstrapper
Run once, right after `flutter create`. It:

- 📁 Creates the base architecture: `app/`, `core/` (constants, di, errors, extensions, helpers, utils, widgets, router, theme), `features/`, and `assets/` (images, svgs)
- 📝 Generates ready-to-use boilerplate files:
  - `main.dart` wired with `BlocObserver` and a `PrefsHelper` initializer
  - `MyApp` root widget pre-configured with `ScreenUtilInit` and light/dark `AppTheme`
  - A basic `AppRoutes` / `RouteNames` router setup
  - `Failure` / `Exception` base classes for error handling
  - A `SharedPreferences` helper (`PrefsHelper`) with get/set/remove for all primitive types
  - `Validators`, `Logger`, `AppColors`, `AppConstants`, and a reusable `CustomTextField` widget
  - A fixed `test/widget_test.dart` that actually matches the generated app (no more failing default counter test)
- 📦 Adds the core dependencies your project needs — **always the latest compatible versions**, never pinned/stale:
  - `flutter_bloc`, `equatable`, `dartz`, `get_it`, `injectable`, `dio`, `shared_preferences`, `flutter_screenutil`, `firebase_core`, `firebase_auth`, `cloud_firestore`
  - Dev: `build_runner`, `injectable_generator`, `flutter_lints`
- 🖼️ Registers `assets/images/` and `assets/svgs/` inside `pubspec.yaml` automatically
- 📥 Runs `flutter pub get` for you at the end
- 🧠 Smart enough to read your package name from `pubspec.yaml` and skip any dependency you already have — safe to re-run

### 2. `genFeature.ps1` — Feature Generator
Run any time you need a new feature. Give it a name, get a full Clean Architecture module:

```
lib/features/<feature_name>/
├── data/
│   ├── datasources/
│   │   ├── remote/   → <feature>_remote_datasource.dart
│   │   └── local/    → <feature>_local_datasource.dart
│   ├── models/
│   └── repositories/ → <feature>_repository_impl.dart
├── domain/
│   ├── entities/     → <feature>_entity.dart
│   ├── repositories/ → <feature>_repository.dart
│   └── usecases/
└── presentation/
    ├── bloc/         → <feature>_bloc.dart, _event.dart, _state.dart
    ├── pages/
    └── widgets/
```

The BLoC files come pre-wired (`Bloc<Event, State>`, initial state, event handler stub) so you can jump straight into writing logic instead of typing class names.

---

## 📋 Requirements

- [Flutter SDK](https://docs.flutter.dev/get-started/install) installed and available in your `PATH`
- Windows with PowerShell **5.1+**, or PowerShell **7+** (cross-platform) on macOS/Linux
- Run scripts from the **root of a Flutter project** (a folder containing `pubspec.yaml`)

> ⚠️ If PowerShell blocks script execution with an "execution policy" error, run this once in an elevated terminal:
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
> ```

---

## 🚀 Getting Started

### 1. Clone this repo
```bash
git clone https://github.com/<your-username>/FlutterArchitect.git
```

### 2. Create your Flutter project (if you haven't already)
```bash
flutter create my_app
cd my_app
```

### 3. Copy the scripts into your project root
Copy `genBaseFolders.ps1` and `genFeature.ps1` from this repo into the root of `my_app` (next to `pubspec.yaml`).

### 4. Bootstrap the architecture
```powershell
.\genBaseFolders.ps1
```
This sets up `core/`, `app/`, `features/`, `assets/`, base files, and installs dependencies.

### 5. Generate a feature whenever you need one
```powershell
.\genFeature.ps1 -FeatureName auth
```
This creates `lib/features/auth/...` with the full data/domain/presentation structure and BLoC files, using `Auth` as the generated class prefix (`AuthBloc`, `AuthRepository`, etc).

You can name features however you like — `auth`, `user_profile`, `home-screen` — the script converts it to a proper Dart `PascalCase` class name automatically.

---

## 📁 Resulting Project Structure

```
lib/
├── app/
│   ├── my_app.dart
│   └── observers/
│       └── app_observer.dart
├── core/
│   ├── constants/
│   ├── di/
│   ├── errors/
│   ├── extensions/
│   ├── helpers/
│   ├── router/
│   ├── theme/
│   ├── utils/
│   └── widgets/
├── features/
│   └── <your_features_here>/
└── main.dart

assets/
├── images/
└── svgs/
```

---

## 🧠 Why this exists

Setting up Clean Architecture by hand for every new project (and every new feature inside it) is repetitive and error-prone — you either copy-paste from an old project or rewrite the same folders and boilerplate again. FlutterArchitect turns that setup into a single command, so the structure stays consistent across every project and every feature.

---

## 🛣️ Roadmap / Ideas

- [ ] `get_it` + `injectable` dependency injection wiring generated automatically per feature
- [ ] Optional `freezed` support for models/states
- [ ] A Bash/Zsh version for full macOS/Linux parity
- [ ] Config file (`architect.config.json`) to customize folder names & dependencies

Contributions and suggestions are welcome — feel free to open an issue or PR.

---

## 🤝 Contributing

1. Fork the repo
2. Create a branch: `git checkout -b feature/your-idea`
3. Commit your changes
4. Open a pull request

---

## 📄 License

This project is open-source. Feel free to add a license of your choice (MIT recommended) via a `LICENSE` file.

---

## 👤 Author

Made by **Hossam** — Flutter Developer.
Connect on [LinkedIn]([https://www.linkedin.com/in/hossam-hesham-b5283b254]) if you'd like to talk Flutter architecture, BLoC, or Clean Architecture patterns.
