# 📚 Book Recommendation System

A Streamlit-based web app that provides book recommendations using a mix of popularity-based, user-based, and item-based collaborative filtering models. This repo demonstrates data preparation, model training, and a polished UI for discovering books and saving favorites.

---

## 🔍 Project Overview

This project builds a practical book recommendation system with three principal recommendation strategies:

- **Popular Books** — Surface the top trending books for all users.
- **User-Based Collaborative Filtering** — Recommend books based on similar users' preferences.
- **Item-Based Collaborative Filtering** — Recommend books similar to a selected book using item similarity.

The app includes a user-friendly Streamlit UI to browse popular books, get user-personalized recommendations, find similar books, and maintain a wishlist.

---

## ⚙️ Features

- Beautiful and responsive Streamlit UI (custom CSS styling)
- Popularity-based top picks
- User-based personalized recommendations
- Item-based (book-to-book) recommendations
- Wishlist (session-based) to save favorite books

---

## 📁 Repository Structure

- `app.py` — Streamlit application (frontend + inference)
- `Data_loader.ipynb` — Notebook that prepares data, trains models and exports artifacts
- `book_details.csv` — Metadata for books (ISBN, title, author, image URL, etc.)
- `users.csv` — User ratings / interactions dataset
- `popularity.csv` — Precomputed popularity ranking (used by Popular tab)
- `item_model.pkl` — Pickled item-item similarity matrix (required by `app.py`)
- `user_model.pkl` — Pickled user-user similarity matrix (required by `app.py`)
- `user_isbn_matrix.pkl` — User × ISBN interaction matrix (required by `app.py`)

> Note: The three `.pkl` model files are produced by running the `Data_loader.ipynb` preprocessing & training notebook.

---

## 🛠️ Installation & Setup

1. Clone the repository:

```bash
git clone https://github.com/<your-username>/book_recommendation.git
cd book_recommendation
```

2. Create and activate a virtual environment (recommended):

```bash
python -m venv venv
source venv/bin/activate  # macOS / Linux
# or
venv\Scripts\activate   # Windows
```

3. Install dependencies:

```bash
pip install streamlit pandas scikit-learn joblib
```

(Optionally create a `requirements.txt` with pinned versions.)

---

## ▶️ Running the App

Make sure the required model & dataset files are present in the project root: `item_model.pkl`, `user_model.pkl`, `user_isbn_matrix.pkl`, `book_details.csv`, `popularity.csv`.

Start the Streamlit app:

```bash
streamlit run app.py
```

Then open the URL printed in the terminal (usually `http://localhost:8501`).

---

## 🧭 How the Models Are Built

Refer to `Data_loader.ipynb` for full data preprocessing and model training steps. In short:

- Clean and merge book metadata and user interactions
- Build a user × ISBN interaction matrix
- Compute user-user similarity (e.g., with cosine similarity)
- Compute item-item similarity for item-based recommendations
- Generate a popularity ranking aggregation and save to `popularity.csv`
- Export `item_model.pkl`, `user_model.pkl`, and `user_isbn_matrix.pkl` for the Streamlit app

---

## 📊 Data Notes

- `book_details.csv` must contain columns such as `ISBN`, `Book-Title`, `Book-Author`, `Image-URL-L` (used by the UI)
- `users.csv` should contain user interactions/ratings that are used to create the `user_isbn` matrix
- `popularity.csv` is a precomputed CSV listing `ISBN`s sorted by popularity; it's used in the Popular tab

---

## ✅ Contribution & Development

- Run `Data_loader.ipynb` to regenerate artifacts after modifying preprocessing or model code
- If adding features, keep the UI responsive and add tests where appropriate
- Open issues / PRs on the Github repo with clear descriptions

---

## 📄 License

This project is released under the MIT License.

---

## 👤 Author

Developed by Aditya Bhardwaj

If you need help setting up or want a feature added, open an issue or contact me via GitHub.
