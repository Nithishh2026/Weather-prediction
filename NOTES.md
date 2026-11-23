# Study Notes: Weather Prediction Project (End to End)

Use these as your own notes. Rewrite sections in your words as you work; professors can tell when you understand something.

---

## Part 1. Where to do what (platforms)

| Purpose | Platform | Why |
|---|---|---|
| Write and run code | **Google Colab** (colab.research.google.com) | Free, nothing to install, runs in browser, free GPU if needed later |
| Same, but on your laptop | **VS Code + Jupyter extension** or Jupyter Notebook | Better for bigger projects; works offline |
| Get data | **Open-Meteo API**, Kaggle, NASA POWER, IMD | Free historical weather data |
| Version control | **Git** (tool) + **GitHub** (website) | Industry standard; recruiters and professors look at your GitHub |
| Write notes | Markdown files (`NOTES.md`), or Notion/Obsidian | Markdown renders nicely on GitHub |
| Resume | Overleaf (LaTeX) or Google Docs / Word | Overleaf is common for research applications |
| Email professors | Your institutional or a professional Gmail | Keep the address clean: firstname.lastname@... |

**Colab vs local:** start in Colab. Move to local VS Code once you are comfortable, because working locally teaches you virtual environments and Git properly.

---

## Part 2. Setup, step by step

### 2.1 Using Colab
1. Go to colab.research.google.com and sign in with Google
2. File > Upload notebook > choose `notebooks/weather_prediction.ipynb`
3. Runtime > Run all
4. The first run downloads data (about 1 minute). Colab's disk resets when the session ends, so download `outputs/` before closing, or mount Google Drive:
```python
from google.colab import drive
drive.mount('/content/drive')
```

### 2.2 Using your laptop (recommended second step)
1. Install Python 3.10+ from python.org
2. Install VS Code, then the "Python" and "Jupyter" extensions
3. Open a terminal in your project folder:
```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```
4. Open the `.ipynb` file in VS Code and pick the `.venv` kernel

**Why a virtual environment?** It keeps this project's library versions separate from other projects, so things do not break later.

---

## Part 3. Git and GitHub from zero

**Concepts**
- **Git** = tracks changes to your files on your computer
- **GitHub** = website that stores your Git repositories online
- **Repository (repo)** = project folder tracked by Git
- **Commit** = a saved snapshot with a message
- **Push** = send commits from laptop to GitHub

### 3.1 One-time setup
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"   # same email as your GitHub account
```
Create a GitHub account at github.com (use a professional username).

### 3.2 Create the repo and push
1. On GitHub: click **New repository**, name it `weather-prediction`, keep it **Public**, do NOT tick "Add README" (you already have one), click Create
2. In your project folder:
```bash
git init
git add .
git commit -m "Initial commit: weather prediction pipeline"
git branch -M main
git remote add origin https://github.com/<your-username>/weather-prediction.git
git push -u origin main
```
3. When asked for a password, GitHub requires a **Personal Access Token** instead: GitHub > Settings > Developer settings > Personal access tokens > generate one with `repo` permission, and paste it as the password. (Or install GitHub CLI and run `gh auth login`.)

### 3.3 Everyday workflow
```bash
git status                       # what changed?
git add .
git commit -m "Add feature importance plot"
git push
```
Write commit messages that say what and why: "Fix data leakage in rolling features", not "update".

### 3.4 Pushing from Colab directly
File > Save a copy in GitHub, then choose the repo. Fine for notebooks, but learn the terminal workflow too.

### 3.5 What a good repo looks like
- Clear README (problem, method, results with numbers, how to run, limitations)
- Notebook that runs top to bottom without errors
- `requirements.txt`, `.gitignore`
- Figures in `outputs/`
- A few meaningful commits over several days (not one giant dump)
- Pin the repo on your GitHub profile

**Before pushing, clear secrets:** never commit API keys, passwords, or personal data.

---

## Part 4. The machine learning concepts (know these cold)

You may be asked about any of these in an interview or in a professor's call.

**Problem types**
- *Classification:* predict a category (rain / no rain)
- *Regression:* predict a number (max temperature)

**Data leakage:** letting the model see information from the future or from the test set. Examples: shuffling time series before splitting; using tomorrow's data as a feature. Leakage inflates scores and is the most common beginner mistake. This project avoids it with a chronological split and features built only from today and earlier.

**Baseline:** a trivial prediction ("tomorrow = today") that any real model must beat. Without a baseline, a score means nothing.

**Class imbalance:** rainy days are fewer than dry days, so a model that always says "no rain" gets high accuracy but is useless. Use precision, recall, F1, ROC-AUC, and `class_weight="balanced"`.

**Metrics**
- *Precision:* of the days I predicted rain, how many were rainy
- *Recall:* of the rainy days, how many did I catch
- *F1:* balance of both
- *ROC-AUC:* how well the model ranks rainy days above dry days
- *MAE:* average absolute error in degrees C
- *RMSE:* like MAE but punishes big errors more
- *R2:* share of variance explained

**Feature engineering:** lags (yesterday's values), rolling averages (trend), cyclical encoding of the day of year (sin/cos so December 31 is "close" to January 1), pressure change (falling pressure often precedes rain).

**Models used**
- *Logistic / Ridge Regression:* simple, interpretable linear models
- *Random Forest:* many decision trees averaged; handles non-linear effects
- *Gradient Boosting:* trees built one after another, each fixing the previous errors

**Time-series cross-validation:** each fold trains on the past and tests on the next block, never the reverse.

**Overfitting:** the model memorises training data and fails on new data. Watch the gap between train and test scores.

---

## Part 5. Working plan (about 3 weeks, adjust to your time)

| Days | Task |
|---|---|
| 1-2 | Set up Colab, run the notebook as given, read every cell |
| 3-4 | Learn Git, create the GitHub repo, push the project |
| 5-8 | Understand each step; change one thing at a time (different threshold, extra features) and record what happens |
| 9-12 | Extension: a second city, or an LSTM, or a Streamlit app |
| 13-15 | Fill in README results, write conclusions in the notebook |
| 16-18 | Write a 2-page project report (PDF) |
| 19-21 | Resume, LinkedIn, professor list, first emails |

**Keep a log** (`NOTES.md` or a notebook of your own): what you tried, what failed, what you learned. This becomes your interview material.

---

## Part 6. Ideas to make the project stronger

Pick one or two; depth beats breadth.
1. **Compare cities:** train on Hyderabad, test on another city. Does it transfer?
2. **Add an LSTM** and honestly report whether it beats Random Forest (often it does not on small data, and saying so is fine)
3. **Probability calibration:** are "70% rain" predictions right 70% of the time?
4. **Compare to a professional forecast:** Open-Meteo also serves forecast model data
5. **Error analysis:** in which months or conditions does the model fail?
6. **Streamlit app:** let a user enter today's values and see the prediction
7. **Extreme events:** focus on heavy rainfall days (a real research problem in India)

---

## Part 7. Glossary
- **API:** a way for programs to request data from a service
- **DataFrame:** table in pandas
- **Kernel:** the Python process running your notebook
- **Hyperparameter:** a setting you choose before training (for example, number of trees)
- **Train/test split:** data used to learn vs data used to evaluate
- **Persistence forecast:** predicting no change from today
