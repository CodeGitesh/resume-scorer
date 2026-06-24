# ⚡ ResumeIQ | AI-Powered Candidate OS

ResumeIQ is a comprehensive, advanced Candidate Operating System designed to help job seekers parse, analyze, grade, match, and enhance their resumes. It features a complete **VMock-style ATS report suite**, an **interactive structured resume editor with live PDF rendering**, a **semantic job matcher**, a **Jobscan-style keyword alignment tool**, and a **Kanban application tracker (CRM)**.

The platform is built using **Streamlit** for the frontend, **SQLite** for persistence, and **Groq Cloud API (Llama-3.3-70b)** for generating state-of-the-art resume enhancements and insights.

---

## 🌟 Core Features in Detail

### 1. 📊 VMock-style ATS Report & Insights (`pages/1_🏠_Candidate_Portal.py`)
* **ATS Gauge Chart**: A polar projection matplotlib chart displaying the candidate's general resume health score out of 100.
* **Breakdown Bar Chart**: Horizontal bar chart breaking down sub-scores for Skills, Action Verbs, Metrics Density, Section Completeness, and Formatting rules.
* **Radar Skill analysis**: A polar radar chart mapping matched skills across five domains (Languages, Frameworks, Cloud & DevOps, Data & ML, Soft Skills).
* **Word Cloud**: Visualizes the density and relevance of keywords found in the parsed text.
* **Salary Estimator**: Provides a localized compensation estimate based on the candidate's target title and parsed experience level.
* **Action Verb Suggestions**: Automatically flags passive language (e.g. *worked on*, *helped*) and suggests high-impact alternatives (e.g. *spearheaded*, *architected*).
* **LinkedIn Bio Generator**: Drafts a professional summary ready to copy-paste into social profiles.
* **Personalized Interview Preparation**: Compiles a tailored list of behavioral and technical questions based on the candidate's structural skill gaps.

### 2. ✍️ Interactive Resume Editor & Live PDF Previewer (`pages/1_🏠_Candidate_Portal.py`)
* **Structured UI Expanders**: Replaced plain-text blocks with dynamic expanders for Skills, Experience, Education, Projects, and Achievements.
* **Dynamic Add/Delete Controls**: Add or remove entries/bullets dynamically across all fields in real-time.
* **Multi-tier Education Grade Selector**: Add grades using dropdowns for CGPA, GPA, Class 10/12 %, and Percentage.
* **Individual AI Card Enhancers**: Focus-enhance a single project card or achievement bullet instantly using Groq Llama-3.3 (following the Google X-Y-Z formula: *Accomplished [X] as measured by [Y], by doing [Z]*) without changing the rest of the document.
* **Layout & Style Engine**: Customize layouts (Clean, Classic, Vivid, Modern Two-Column), font families (Helvetica, Times, Courier), page sizes, page margins, and color palettes (Dark Blue, Black, Green, Slate Grey, Crimson Red).
* **Live PDF Compiler**: Instantly compiles code changes into a professional PDF layout in the background using `fpdf2` and renders it inside an in-browser iframe.

### 3. 🤖 Groq AI Resume Optimizer (`pages/1_🏠_Candidate_Portal.py`)
* **Selected Section Enhancement**: Enhances specific checked sections (Summary, Skills, Experience, Education, Projects, Achievements) via Llama-3.3.
* **Quantifiable Metrics Questionnaire**: Optional form prompts candidates for quantifiable metrics (regarding outcomes, scale, tools, team sizes) which are incorporated into the prompt to guide the LLM in outputting highly quantified, metrics-driven bullet points.

### 4. 💼 Job Matches & Tailored Cover Letters (`pages/2_💼_Job_Matches.py`)
* **Semantic Recommendation Engine**: Matches candidate resumes against the database job corpus using TF-IDF and Cosine Similarity.
* **Cover Letter Generator**: Generates a tailored Cover Letter PDF aligning candidate skills with the recommended position.

### 5. 🎯 Custom Job Scanner (`pages/5_🎯_Jobscan_Matcher.py`)
* Paste a custom target job description to run a TF-IDF scan showing match percentages, matching keywords, and missing gap keywords.

### 6. 📋 Application Tracker CRM (`pages/6_📋_Application_Tracker.py`)
* Kanban-style pipeline tracking job applications through stages (Applied, Interviewing, Offered, Rejected) to maintain an organized job search pipeline.

### 7. 👔 HR Admin Dashboard & Global Leaderboard (`pages/3_👔_HR_Dashboard.py` & `pages/7_🏆_Leaderboard.py`)
* Candidate directory detailing scores, formatting issues, experience levels, and predicted job categories alongside global statistics.

---

## 🧠 Machine Learning Models & NLP Algorithms

### 1. Job Role Classifier (Multinomial Naive Bayes)
* **Objective**: Predicts the candidate's job category/domain from raw resume text.
* **Implementation**: Uses a `MultinomialNB` classifier from `scikit-learn` trained on cleaned resume text features extracted using a `TfidfVectorizer` (set to `max_features=1500` and filtering English stop words).
* **Pipeline**: Text cleaning → TF-IDF Vectorization → Naive Bayes classification.

### 2. Bullet Point Impact Classifier (Multinomial Naive Bayes)
* **Objective**: Evaluates whether individual resume bullet points are strong (quantified, action-verb driven) or weak (passive, task-oriented).
* **Implementation**: Uses a `MultinomialNB` model trained on a curated corpus of labeled bullet points. Featurized using a `TfidfVectorizer` (set to `max_features=500` and filtering English stop words).
* **Outputs**: Label (`Strong` or `Weak`) and confidence score.

### 3. Heuristic ATS Health Regressor
* **Objective**: Computes the candidate's general resume health score (0-100).
* **Formula**:
  $$\text{ATS Score} = (S \times 0.30) + (V \times 0.20) + (M \times 0.20) + (C \times 0.15) + (F \times 0.15)$$
  Where:
  * $S$ (Skills Score) = Min(100, Number of recognized skills / 20 * 100)
  * $V$ (Action Verbs Score) = Min(100, Number of strong action verbs / 10 * 100)
  * $M$ (Metrics Score) = Min(100, Number of quantified metrics / 5 * 100)
  * $C$ (Section Completeness Score) = (Count of parsed sections / 4) * 100
  * $F$ (Formatting Score) = Max(0, 100 - Formatting issues count * 15)

### 4. Semantic Job Matcher (Cosine Similarity with TF-IDF)
* **Objective**: Matches resumes against job descriptions based on semantic content.
* **Implementation**:
  * Texts are preprocessed using `nltk`'s `PorterStemmer` and alphanumeric tokenization (filtering custom short stop words).
  * Document vectors are generated using a `TfidfVectorizer` configured with sublinear term-frequency scaling (`sublinear_tf=True`) to reduce the impact of repeated words.
  * Similarity is computed using **Cosine Similarity** between the resume vector and the job corpus vectors.
  * **Relative Max-Relative Scaling**: Scores are scaled relative to the best raw matching score in the database (ensuring the top candidate gets ~96% match while preventing low-similarity matches from inflating).

### 5. Domain Centroid Scoring
* **Objective**: Measures structural alignment of the candidate's resume with a target industry domain.
* **Implementation**: Calculates the average vector (centroid) of all job descriptions belonging to the candidate's predicted domain, and computes the cosine similarity between the resume vector and this centroid.

---

## 🛠️ Tech Stack

* **Frontend App Server**: Streamlit
* **AI/LLM Orchestration**: Groq API (`llama-3.3-70b-versatile`)
* **Vector Embeddings & NLP**: Scikit-Learn, NLTK
* **Data Visualization**: Matplotlib
* **PDF Engine**: FPDF2
* **Database**: SQLite3
* **Text Extraction**: PyMuPDF (fitz)

---

## 🚀 Installation & Local Setup

### 1. Clone the Repository
```bash
git clone https://github.com/srbhr/Resume-Matcher.git
cd resume-scorer
```

### 2. Set Up a Virtual Environment
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables
Create a `.env` file in the root directory and add your Groq API key:
```env
GROQ_API_KEY=your_groq_api_key_here
DB_PATH=data/jobs.db
```

### 5. Run the Streamlit Application
```bash
python3 -m streamlit run app.py --server.port 8502
```

Open your browser and navigate to `http://localhost:8502` to access the portal.

---

## 📂 Project Directory Structure

```text
├── app.py                       # Main application gateway and navigation router
├── auth_utils.py                # User login/registration security functions
├── database.py                  # SQLite database controller and application schema
├── analyzer.py                  # Text extraction, NLP skills processing, and formatting checks
├── resume_builder.py            # FPDF compiler, page spacing handlers, and layout templates
├── llm_utils.py                 # Groq API client, structured JSON parser, and formatting helpers
├── pages/                       # Multi-page application views
│   ├── 1_🏠_Candidate_Portal.py  # ATS analyzer, interactive editor, and PDF styles panel
│   ├── 2_💼_Job_Matches.py       # Recommendation pipeline and Cover Letter builder
│   ├── 3_👔_HR_Dashboard.py      # Enterprise candidate administration view
│   ├── 4_🎙️_Interview_Grader.py  # Audio/interactive questionnaire grader
│   ├── 4_🔍_Job_Search_Engine.py  # Semantic job search explorer
│   ├── 5_🎯_Jobscan_Matcher.py   # Job description matching comparison scanner
│   ├── 5_📈_Evaluation_Metrics.py# Model search evaluation statistics
│   ├── 6_📋_Application_Tracker.py# Job application Kanban CRM
│   └── 7_🏆_Leaderboard.py        # Global candidate ATS score tracking
└── data/                        # SQLite files and saved candidate PDF uploads
```
