

# 🎓 PokeQuest / One Piece Grand Line: The Gamified Study App

## 🎯 Project Overview

This is a full-stack gamified study application designed to make learning (Physics, Chemistry, Mathematics) more engaging by integrating themes and mechanics from popular media (Pokémon and One Piece).

The application combines a focused **Pomodoro timer** with AI-generated, subject-specific quizzes. User performance directly impacts their **Trainer Level / Bounty** and the **Evolution Status / Nakama Status** of their partner. The backend utilizes the **Gemini API** for dynamic, personalized quiz content generation.

### Key Features

  * **Dual Theming:** Seamlessly switch between **Pokémon (Kalos Quest)** and **One Piece (Grand Line)** themes.
  * **AI-Powered Quizzing:** Quizzes are generated using the **Gemini API** based on the user's selected subject, difficulty (derived from Level), and identified **Weak Topics** from past performance.
  * **Gamification Loop:** Users gain XP and levels, influencing their in-app status and the evolution of their partner.
  * **Pomodoro Integration:** Users must enter a Pomodoro **"Active Training Session"** to access quizzes, promoting focused study.
  * **Personalized Feedback:** Provides an AI-generated study report and actionable advice based on the weak topics identified in the last quiz session.
  * **Leaderboard:** Displays global user rankings by total XP.
  * **Resources & To-Do List:** Curated external resources and a task manager integrated using the Google Keep API (mocked).

-----

## 🛠️ Tech Stack

| Component | Technology | Details |
| :--- | :--- | :--- |
| **Frontend** | **React (with TypeScript)** | Single-page application built with modern functional components and hooks. |
| **Styling** | **Tailwind CSS** | Utility-first framework for rapid, highly themed UI development. |
| **Backend** | **Flask (Python)** | Lightweight REST API server. |
| **Database** | **SQLite** | Simple file-based database for user data persistence. |
| **AI/LLM** | **Gemini API** (`gemini-2.5-flash`) | Used for reliable, structured generation of multiple-choice questions. |
| **Auth** | **JWT / Custom Token** | Hashing passwords with `werkzeug.security` and secure Bearer token generation. |

-----

## 🚀 Getting Started

### Prerequisites

1.  **Python 3.8+** and `pip`
2.  **Node.js** and `npm` or `yarn`
3.  **Gemini API Key:** You must have a Google AI Studio account and an API key.

### 1\. Backend Setup

1.  **Clone the repository and navigate to the project root.**
2.  **Set up the Python Virtual Environment:**
    ```bash
    python3 -m venv venv
    source venv/bin/activate  # On Windows use: venv\Scripts\activate
    ```
3.  **Install Python dependencies:**
    ```bash
    pip install Flask Flask-CORS requests Werkzeug sqlite3
    ```
4.  **Configure API Key:**
    In the backend file (`app.py` in a typical setup, or where the Flask code resides), replace the placeholder with your actual Gemini API Key:
    ```python
    API_KEY = "YOUR_GEMINI_API_KEY_HERE"
    ```
5.  **Run the Flask Server:**
    ```bash
    python3 backend_file_name.py  # e.g., python3 app.py
    ```
    The server should start on `http://127.0.0.1:5000`.

### 2\. Frontend Setup

1.  **Install Node dependencies:**
    ```bash
    npm install
    # or yarn install
    ```
2.  **Run the React Development Server:**
    ```bash
    npm run dev
    # or yarn dev
    ```
    The frontend should start on `http://localhost:3000` (or similar).

-----

## 📂 Project Structure

Although the provided code is monolithic, here is how the structure is logically separated:

### Frontend (React/TypeScript)

| Component / Hook | Role |
| :--- | :--- |
| `App` | Main application router (`landing`, `login`, `main`). |
| `useAuth` | Handles authentication state, `localStorage` persistence, and theme selection. |
| `MainLayout / OnePieceMainLayout` | Manages global logged-in state (Pomodoro timer, screen routing). |
| `Dashboard` | Displays Trainer/Nakama stats, XP, Level, and Pomodoro controls. |
| `QuizBattle` | Handles API interaction for quiz generation, manages question flow and timer. |
| `FeedbackScreen` | Displays the personalized AI report and features Text-to-Speech. |
| `TodoListScreen` | Handles mock integration with the external lists tool. |
| `LoginScreen` | Manages user registration/login and partner selection. |

### Backend (Flask/SQLite)

| Endpoint | Method | Function |
| :--- | :--- | :--- |
| `/api/register` | `POST` | Creates a new user entry in the `users` table. |
| `/api/login` | `POST` | Generates a secure `auth_token` on successful login. |
| `/api/dashboard` | `GET` | Fetches user gamification stats (XP, Level, Weak Topics). |
| `/api/generate_quiz` | `POST` | **Crucial:** Calls Gemini with a personalized prompt (difficulty, weak topics) and structured output schema. Implements retries with exponential backoff. |
| `/api/submit_quiz` | `POST` | Updates user XP, calculates new Level/Badges, and logs quiz history. |
| `/api/leaderboard` | `GET` | Returns the top 10 trainers/pirates by total XP. |

-----

## 📝 API Logic and Gamification Details

### XP and Leveling Formula

The progression system is based on linear scaling:

  * **XP Gained per Correct Answer:** $\text{30 XP}$
  * **XP Required per Level:** $\text{300 XP}$
  * **Level Calculation:** $\text{New Level} = 1 + (\text{Total XP} // \text{300})$
  * **Difficulty Scaling:**
      * $\text{Level 1-3}$: **Intermediate (High School)** Difficulty
      * $\text{Level 4+}$: **Advanced (College)** Difficulty

### Gemini API Resilience

The Python function `gemini_api_call` is designed for high reliability:

```python
# Retry mechanism in Python backend:
MAX_RETRIES = 5
# Retries with exponential backoff for common transient errors
if response.status_code in [429, 500, 503]:
    delay = 2 ** i
    time.sleep(delay)
```

This prevents quiz generation failure due to temporary API service issues.

### Quiz Content Prompting

The backend ensures personalized content by formulating the prompt to Gemini:

  * It dynamically sets the **`subject`**, **`difficulty`**, and a prioritized list of **`weak_topics`** (up to 3 out of 5 questions must focus on these areas).
  * It enforces the academic quality and the strict JSON output structure required by the frontend.

-----

## 🎨 Design & Theming

The frontend design is powered by Tailwind CSS to create two distinct, high-fidelity gaming environments:

1.  **Pokémon (Kalos Quest):** Features vibrant blue/cyan colors, lightning bolts (`Zap`), Poké Balls, and trainer/gym metaphors.
2.  **One Piece (Grand Line):** Uses strong red/yellow/black colors, skull and crossbones, and pirate metaphors (Bounty, Nakama, Logbook).

The user's initial selection on the login screen determines the permanent aesthetic and terminology for their in-game experience.

## 🤝 Contributing

This project is currently a self-contained learning tool. Feel free to fork it, experiment with the features, or extend the logic:

  * **Add New Subjects:** Expand `SubjectData` in the React frontend and update the Gemini prompt to handle new subjects like History or Computer Science.
  * **Improve Gamification:** Implement the streak calculation logic in `/api/submit_quiz`.
  * **Advanced Leveling:** Introduce an exponential curve for XP requirements.

