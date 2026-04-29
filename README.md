<div align="center">

# 🍳 AI Recipe

### Transform your cooking with personalized, AI-generated recipes tailored to your tastes and ingredients

[![Flutter](https://img.shields.io/badge/Flutter-3.x-02569B?logo=flutter)](https://flutter.dev)
[![Dart](https://img.shields.io/badge/Dart->=3.3.3-0175C2?logo=dart)](https://dart.dev)
[![Gemini](https://img.shields.io/badge/Google_Gemini-AI-4285F4?logo=google)](https://ai.google.dev)
[![Firebase](https://img.shields.io/badge/Firebase-Firestore-FFCA28?logo=firebase)](https://firebase.google.com)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

**Snap a photo of your ingredients — get a personalized recipe in seconds, powered by Google Gemini.**

</div>

---

## 📖 Overview

**AI Recipe** is a Flutter app that uses **Google Gemini's multimodal AI** to generate personalized recipes from photos of your ingredients. Simply photograph what's in your fridge, optionally filter by cuisine type, dietary restrictions, and staple ingredients, and let Gemini craft a tailored recipe complete with instructions, nutritional info, allergen warnings, and a travel-inspired story behind the dish.

Generated recipes can be saved to **Firebase Firestore** for later reference, rated, and managed from a dedicated saved recipes screen.

---

## ✨ Features

| Feature | Description |
|---|---|
| 📸 **Image-to-Recipe** | Photograph ingredients and get a full recipe via Gemini Vision |
| ✍️ **Text-Only Mode** | Generate recipes from text descriptions without an image |
| 🍜 **Cuisine Filters** | Filter by cuisine type (Italian, Asian, Mexican, and more) |
| 🥗 **Dietary Restrictions** | Specify vegan, gluten-free, dairy-free, and other restrictions |
| 🧂 **Staple Ingredients** | Tag common pantry items (oil, salt, eggs, etc.) to include |
| 💾 **Save Recipes** | Persist favourites to Firestore and access them anytime |
| ⭐ **Recipe Rating** | Rate saved recipes with a star rating |
| 🧾 **Full Prompt Viewer** | Inspect the exact prompt sent to Gemini before submission |
| 📊 **Nutrition Info** | Per-serving calories, fat, carbs, and protein in every recipe |
| ⚠️ **Allergen Warnings** | Automatic allergen identification in generated recipes |
| 📱 **Adaptive Layout** | Responsive UI for both mobile and tablet/desktop |

---

## 🏗️ Architecture

The project follows **MVVM** with a Service Layer:

```
View  (Screens & Widgets)
  ↓ watches
ViewModel  (PromptViewModel, SavedRecipesViewModel) — ChangeNotifier
  ↓ calls
Service Layer  (GeminiService, FirestoreService)
  ↓ uses
External APIs  (Google Gemini API, Firebase Firestore)
```

### Key Files

```
lib/
├── main.dart                          # App entry, Gemini model init, Provider setup
├── router.dart                        # Adaptive routing (mobile/desktop)
├── theme.dart                         # App-wide theme & spacing constants
├── features/
│   ├── prompt/
│   │   ├── prompt_model.dart          # PromptData — user input state
│   │   ├── prompt_view_model.dart     # PromptViewModel — Gemini orchestration
│   │   ├── prompt_screen.dart         # Main recipe generation UI
│   │   └── widget/                    # Image input, full prompt dialog
│   └── recipes/
│       ├── recipe_model.dart          # Recipe — Gemini JSON ↔ Firestore
│       ├── recipes_view_model.dart    # SavedRecipesViewModel
│       ├── saved_recipes_screen.dart  # Saved recipes list UI
│       └── widget/                    # Recipe card, fullscreen dialog
├── services/
│   ├── gemini.dart                    # GeminiService — text & multimodal calls
│   └── firestore.dart                 # FirestoreService — CRUD operations
└── util/
    ├── filter_chip_enums.dart         # Cuisine, ingredient & dietary enums
    ├── json_parsing.dart              # Cleans Gemini JSON response
    └── extensions.dart                # Responsive layout helpers
```

---

## 🤖 How It Works

1. **User builds a prompt** — adds ingredient photos, selects filters, types extra context
2. **`PromptViewModel.buildPrompt()`** assembles a structured prompt with formatting instructions
3. **`GeminiService`** sends the prompt to either `gemini-pro-vision` (with images) or `gemini-pro` (text only)
4. **Gemini returns structured JSON** matching the `Recipe` schema
5. **`Recipe.fromGeneratedContent()`** parses the JSON into a typed model
6. **User can save** the recipe to **Firestore** via `FirestoreService`

### Gemini Models Used

| Model | Used For |
|---|---|
| `gemini-pro-vision` | Prompts that include ingredient images |
| `gemini-pro` | Text-only prompts without images |

---

## 🚀 Getting Started

### Prerequisites

- Flutter SDK `>=3.x`
- Dart `>=3.3.3 <4.0.0`
- A [Google AI Studio](https://aistudio.google.com) API key
- A Firebase project with Firestore enabled

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/nmustakim/ai-recipe.git
cd ai-recipe

# 2. Install dependencies
flutter pub get

# 3. Configure Firebase
# Place google-services.json in android/app/
# Place GoogleService-Info.plist in ios/Runner/

# 4. Run with your Gemini API key
flutter run --dart-define=API_KEY=YOUR_GEMINI_API_KEY
```

> ⚠️ The app reads the API key via `String.fromEnvironment('API_KEY')` — never hardcode your key in source code.

### Build Release

```bash
# Android
flutter build apk --release --dart-define=API_KEY=YOUR_GEMINI_API_KEY

# iOS
flutter build ios --release --dart-define=API_KEY=YOUR_GEMINI_API_KEY
```

---

## 🗂️ Data Model

### `PromptData`
Holds the user's current input state before submission:
- `images` — list of ingredient photos (`XFile`)
- `textInput` — free-text context
- `selectedCuisines` — set of `CuisineFilter`
- `selectedBasicIngredients` — set of `BasicIngredientsFilter`
- `selectedDietaryRestrictions` — set of `DietaryRestrictionsFilter`

### `Recipe`
Parsed from Gemini's JSON response and persisted to Firestore:
- `title`, `description`, `cuisine`, `servings`
- `ingredients`, `instructions` — `List<String>`
- `allergens` — `List<String>`
- `nutritionInformation` — `Map<String, String>` (calories, fat, carbs, protein)
- `rating` — user star rating (default `-1` = unrated)

---

## 🛠️ Tech Stack

| Category | Technology | Version |
|---|---|---|
| Framework | Flutter | 3.x |
| Language | Dart | >=3.3.3 |
| AI | Google Gemini (`google_generative_ai`) | ^0.4.0 |
| Cloud Database | Firebase Firestore | ^4.15.7 |
| State Management | Provider | ^6.1.1 |
| Camera | `camera` + `image_picker` | ^0.10.5 / ^1.0.7 |
| UI | `flutter_markdown`, `google_fonts`, `material_symbols_icons` | latest |

---

<div align="center">

Built with ❤️ using Flutter & Google Gemini

⭐ Star this repo if you found it useful!

</div>
