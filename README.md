# 📚 Book Recommendation System

A production-ready Streamlit-based book recommendation application that leverages collaborative filtering algorithms to provide personalized book suggestions. This system combines three complementary recommendation strategies to deliver diverse and relevant recommendations.

---

## 🎯 Project Overview

This Book Recommendation System demonstrates the practical application of machine learning in e-commerce and content discovery. It uses **collaborative filtering** techniques to analyze user behavior and book characteristics, enabling the system to:

- **Surface trending books** to all users based on popularity metrics
- **Provide personalized recommendations** by identifying users with similar taste profiles
- **Find similar books** using item-based collaborative filtering
- **Maintain user wishlists** for session-based favorites tracking

The application features a modern, responsive Streamlit UI with custom CSS styling that provides an intuitive browsing and discovery experience similar to e-commerce platforms.

---

## 🤖 Machine Learning Models

### 1. **Popularity-Based Recommendation**
- **Algorithm**: Weighted popularity score
- **Metric**: Combination of rating count and average rating
- **Use Case**: Cold-start recommendations for new or anonymous users
- **Implementation**: Pre-computed popularity ranking stored in `popularity.csv`

### 2. **User-Based Collaborative Filtering**
- **Algorithm**: K-Nearest Neighbors (KNN) with cosine similarity
- **Metric**: Cosine similarity between user rating vectors
- **How it works**:
  1. Creates a User × ISBN rating matrix
  2. Computes pairwise cosine similarity between all users
  3. For a target user, finds 5 most similar users
  4. Recommends books highly rated by these similar users
- **Model Artifact**: `user_model.pkl` (User similarity matrix)
- **Advantages**: Captures user preferences and collaborative patterns
- **Limitations**: Suffers from cold-start problem for new users

### 3. **Item-Based Collaborative Filtering**
- **Algorithm**: Content-aware similarity using rating patterns
- **Metric**: Cosine similarity between item rating vectors
- **How it works**:
  1. Creates an ISBN × User rating matrix (transposed from user-based)
  2. Computes pairwise cosine similarity between all books
  3. For a selected book, retrieves the 6 most similar books
  4. Returns books with highest similarity scores
- **Model Artifact**: `item_model.pkl` (Item similarity matrix)
- **Advantages**: Effective for content discovery and "similar items" features
- **Limitations**: Requires items to have been rated by users

### 4. **Data Filtering & Preprocessing**
- **Active users filter**: Retains only users with 30+ ratings to ensure meaningful preferences
- **Popular books filter**: Keeps only books with 50+ ratings for statistical significance
- **Data validation**: Filters only positive ratings (> 0) to exclude implicit dislikes

---

## 📊 Data Architecture

### Input Data Format

**Books Dataset** (`books.csv`)
```
ISBN, Book-Title, Book-Author, Publisher, Year-Of-Publication, Image-URL-L
```

**Ratings Dataset** (`ratings.csv`)
```
User-ID, ISBN, Book-Rating (1-10 scale)
```

**Users Dataset** (`users.csv`)
```
User-ID, Location, Age
```

### Output Data Format

**User-ISBN Rating Matrix** (`user_isbn_matrix.pkl`)
- Dimensions: Users × ISBN
- Values: Rating scores (0-10, 0 for unrated)
- Sparsity: Typically >99% sparse (most users haven't rated most books)

**Item Similarity Matrix** (`item_model.pkl`)
- Dimensions: ISBN × ISBN
- Values: Cosine similarity scores (0-1 range)
- Interpretation: Higher values indicate more similar books

**User Similarity Matrix** (`user_model.pkl`)
- Dimensions: User-ID × User-ID
- Values: Cosine similarity scores (0-1 range)
- Interpretation: Higher values indicate users with similar preferences

**Popularity Scores** (`popularity.csv`)
- Columns: ISBN, rating_count, avg_rating
- Sorted by rating_count descending, then avg_rating descending

---

## ⚙️ Features & Functionality

### 🏠 Home Tab
- Welcome screen with system overview
- Quick navigation guide

### 🔥 Popular Tab
- Displays top 12 trending books across all users
- Ranked by rating count and average rating
- Ideal for new user onboarding and trend discovery

### 👤 User-Based Recommendations Tab
- Select any user ID from dropdown
- System finds 5 most similar users based on rating patterns
- Recommends 6 books most highly rated by similar users
- Perfect for personalization based on user profiles

### 📖 Item-Based Recommendations Tab
- Search for any book by title
- System computes similarity with selected book
- Returns 6 most similar books for discovery
- Great for "customers who viewed X also viewed Y" scenarios

### ❤️ Wishlist Tab
- Session-based wishlist management
- Save favorite books across all recommendation tabs
- View all saved books in one place

### 🎨 UI/UX Features
- Responsive design with gradient headers
- Book cards with cover images, titles, and authors
- Quick Google search links for each book
- Rating display and interaction buttons
- Mobile-friendly layout with responsive columns

---

## 🛠️ Technical Implementation

### Dependencies
```
streamlit          # Web framework for interactive UI
pandas             # Data manipulation and analysis
numpy              # Numerical computations
scikit-learn       # Cosine similarity calculations
joblib             # Efficient model serialization
```

### Model Training Pipeline

1. **Data Loading** (`Data_loader.ipynb`)
   - Loads large CSV files in chunks (100k rows per chunk)
   - Merges books, ratings, and users datasets

2. **Data Cleaning**
   - Removes duplicate books (keep unique ISBN)
   - Filters out non-positive ratings
   - Merges rating data with book metadata

3. **Filtering for Quality**
   - Removes inactive users (< 30 ratings)
   - Removes unpopular books (< 50 ratings)
   - Ensures statistically meaningful patterns

4. **Feature Engineering**
   - Creates User × ISBN rating matrix
   - Creates ISBN × User rating matrix
   - Computes popularity metrics

5. **Similarity Computation**
   - **Cosine Similarity** formula: $\cos(\theta) = \frac{\mathbf{A} \cdot \mathbf{B}}{|\mathbf{A}| |\mathbf{B}|}$
   - Applied to both user and item rating vectors
   - Results in symmetric similarity matrices (0-1 range)

6. **Model Serialization**
   - All matrices pickled for fast loading in production
   - Enables sub-second inference latency

### Inference Pipeline (`app.py`)

1. **Load Models** (cached with `@st.cache_data`)
   - item_model.pkl → Item similarity matrix
   - user_model.pkl → User similarity matrix
   - user_isbn_matrix.pkl → User ratings matrix
   - popularity.csv → Precomputed rankings

2. **Recommendation Logic**
   ```
   For User-Based:
   - Input: User ID
   - Find: Top-5 similar users using user_sim
   - Aggregate: Average ratings from similar users
   - Return: Top-6 highest-rated books
   
   For Item-Based:
   - Input: Book ISBN
   - Lookup: Similarity row in item_sim
   - Sort: By similarity score descending
   - Return: Top-6 most similar books
   ```

3. **Display Results**
   - Fetch book metadata from `book_meta` dictionary
   - Render book cards with images and details
   - Enable user interactions (save, search)

---

## 📁 Repository Structure

```
book_recommendation_system/
├── app.py                      # Streamlit application (UI + inference)
├── Data_loader.ipynb          # Data prep & model training notebook
│
├── Data Sources (Input):
│   ├── book_details.csv       # Book metadata (ISBN, title, author, image URL)
│   ├── users.csv              # User information and interactions
│   └── popularity.csv         # Pre-computed popularity rankings
│
├── Trained Models (Output):
│   ├── item_model.pkl         # ISBN × ISBN similarity matrix
│   ├── user_model.pkl         # User × User similarity matrix
│   └── user_isbn_matrix.pkl   # User × ISBN rating matrix
│
├── Documentation:
│   ├── README.md              # This file
│   └── README copy.md         # Alternative documentation
```

---

## 🚀 Quick Start

### Installation
```bash
# Clone repository
git clone <repository-url>
cd book_recommendation_system

# Install dependencies
pip install -r requirements.txt
```

### Training the Models
```bash
# Open Data_loader.ipynb in Jupyter/Colab
# Update paths to your Books.csv, Ratings.csv, Users.csv
# Run all cells
# This generates: item_model.pkl, user_model.pkl, user_isbn_matrix.pkl
```

### Running the Application
```bash
# Start Streamlit app
streamlit run app.py

# Open http://localhost:8501 in browser
```

---

## 📈 Performance Metrics

### Data Statistics (Post-filtering)
- **Active Users**: ~5,000+ users with 30+ ratings each
- **Popular Books**: ~1,700+ books with 50+ ratings each
- **Sparsity**: >99% sparse rating matrix
- **Similarity Matrix Size**: ~1,700 × 1,700 (item similarity)

### Inference Performance
- **Model Load Time**: <1 second (cached)
- **Recommendation Generation**: <100ms per request
- **UI Response Time**: <500ms end-to-end

### Recommendation Quality
- **Coverage**: System can recommend any of the ~1,700 books
- **Diversity**: Item-based filtering provides different results than user-based
- **Precision**: Depends on user/item sparsity and similarity threshold

---

## 🔍 Algorithm Details

### Cosine Similarity Explanation
Cosine similarity measures the angular distance between two rating vectors:
- **Score Range**: 0 (no similarity) to 1 (identical)
- **Interpretation**: Measures user preference alignment, not absolute rating values
- **Robustness**: Unaffected by rating scale differences

### Matrix Operations
```python
# Item-based similarity
item_similarity = cosine_similarity(isbn_user_matrix)
# Result: How similar are books based on who rated them?

# User-based similarity
user_similarity = cosine_similarity(user_isbn_matrix)
# Result: How similar are users based on what they rated?
```

---

## 🎓 Machine Learning Concepts

1. **Collaborative Filtering**
   - Assumes: Users with similar past behavior will have similar future preferences
   - No content analysis needed; purely behavior-based

2. **Content-Agnostic Approach**
   - System doesn't analyze book descriptions, genres, or keywords
   - Works purely from rating patterns (why it's collaborative)

3. **Sparsity Challenge**
   - Most users rate <0.1% of available books
   - Similarity metrics still effective due to high user/item overlap

4. **Cold-Start Problem**
   - New users: Use popularity-based recommendations
   - New books: No user similarity data available; popularity fallback

---

## 🔧 Customization & Extension

### Adding New Recommendation Strategies
1. **Content-Based Filtering**: Analyze book features (genre, author, keywords)
2. **Hybrid Approach**: Combine collaborative + content-based
3. **Deep Learning**: Neural collaborative filtering (embedding-based)
4. **Time-Decay**: Weight recent ratings more heavily

### Improving Recommendations
- **Implicit Feedback**: Track page views, saves, clicks
- **Contextual Data**: Time of day, user demographics
- **A/B Testing**: Compare algorithm performance
- **Feedback Loop**: Collect user ratings to retrain models

### Scaling to Production
- **Batch Prediction**: Pre-compute recommendations for all users
- **Real-Time Updates**: Incremental model updates with new ratings
- **Caching**: Store frequently requested recommendations
- **Distributed Processing**: Use Spark for large-scale similarity computation

---

## 📚 References & Resources

- **Cosine Similarity**: [Scikit-learn Documentation](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.pairwise.cosine_similarity.html)
- **Collaborative Filtering**: [GroupLens Research Papers](https://grouplens.org/papers/)
- **Streamlit Documentation**: [Streamlit Docs](https://docs.streamlit.io/)
- **Book Dataset Source**: [Book Crossing Dataset](http://www2.informatik.uni-freiburg.de/~cziegler/BX/)

---

## 📝 License & Attribution

This project is educational and demonstrates practical ML recommendation systems. Adapt and use as needed for your own projects.

---

## 👤 Author Notes

Built as a complete end-to-end recommendation system with:
- ✅ Data preprocessing & validation
- ✅ Multiple recommendation algorithms
- ✅ Production-ready model serialization
- ✅ Modern interactive UI
- ✅ Session management & state handling

Perfect for learning or deploying personalized book discovery features.