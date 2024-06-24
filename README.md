# Movie-Recommendation-System
import pandas as pd
import numpy as np
# Load data
movies = pd.read_csv('path/to/movies.csv')
ratings = pd.read_csv('path/to/ratings.csv')
# Merge movies and ratings data
movie_ratings = pd.merge(ratings, movies, on='movieId')

# Create a pivot table of user ratings for each movie
ratings_matrix = movie_ratings.pivot_table(index='userId', columns='title', values='rating')

# Calculate correlation matrix
corr_matrix = ratings_matrix.corr(method='pearson', min_periods=5)

# Function to get similar movies
def get_similar_movies(movie_name, rating):
    similar_ratings = corr_matrix[movie_name] * (rating - 2.5)
    similar_ratings = similar_ratings.sort_values(ascending=False)
    return similar_ratings

# Function to recommend movies
def recommend_movies(user_ratings):
    similar_movies = pd.Series()
    for movie, rating in user_ratings:
        similar_movies = similar_movies.append(get_similar_movies(movie, rating))
    
    similar_movies = similar_movies.groupby(similar_movies.index).sum()
    similar_movies = similar_movies.sort_values(ascending=False)
    recommended_movies = similar_movies.drop(user_ratings.index)
    return recommended_movies.head(10)

# Example usage
user_ratings = [("Forrest Gump (1994)", 5), ("Shawshank Redemption, The (1994)", 4), ("Pulp Fiction (1994)", 3)]
recommendations = recommend_movies(user_ratings)
print(recommendations)
