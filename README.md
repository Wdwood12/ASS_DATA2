
Data upload
This is to analyze the most top six recent movie rated by five individuals (family and friends) survey from 1-5.importing from MySQL database.

movies_df <- dbGetQuery(con, "SELECT CAST(movie_id AS SIGNED) AS movie_id, title, release_year FROM movies;")

users_df <- dbGetQuery(con, "SELECT CAST(user_id AS SIGNED) AS user_id, name FROM users;")

ratings_df <- dbGetQuery(con, "SELECT CAST(rating_id AS SIGNED) AS rating_id,
                                  CAST(movie_id AS SIGNED) AS movie_id,
                                  CAST(user_id AS SIGNED) AS user_id,
                                  CAST(rating AS SIGNED) AS rating 
                              FROM ratings;")
Missing Data
ratings_complete <- ratings_df %>%
  left_join(movies_df, by = "movie_id") %>%
  left_join(users_df, by = "user_id")

head(ratings_complete)
##   rating_id movie_id user_id rating          title release_year    name
## 1         1        1       1      3    Rebel Ridge         2024   James
## 2         2        1       2      4    Rebel Ridge         2024  Leyssa
## 3         3        1       3      2    Rebel Ridge         2024  Eugene
## 4         4        1       4      3    Rebel Ridge         2024    Josh
## 5         5        1       5      4    Rebel Ridge         2024 Chantal
## 6         6        3       1      5 Back in Action         2024   James
avg_ratings <- ratings_complete %>%
  group_by(title) %>%
  summarize(avg_rating = mean(rating))
print(avg_ratings)
## # A tibble: 5 × 2
##   title              avg_rating
##   <chr>                   <dbl>
## 1 American Renegades        2.6
## 2 Back in Action            3.4
## 3 Fallen                    3.2
## 4 Rebel Ridge               3.2
## 5 The Menu                  2.6
finalizing
str(movies_df)
## 'data.frame':    18 obs. of  3 variables:
##  $ movie_id    : num  1 2 3 4 5 6 7 8 9 10 ...
##  $ title       : chr  "Rebel Ridge" "Minions 2024" "Back in Action" "Fallen" ...
##  $ release_year: int  2024 2024 2024 2024 2024 2024 2024 2024 2024 2024 ...
str(users_df)
## 'data.frame':    15 obs. of  2 variables:
##  $ user_id: num  1 2 3 4 5 6 7 8 9 10 ...
##  $ name   : chr  "James" "Leyssa" "Eugene" "Josh" ...
str(ratings_df)
## 'data.frame':    50 obs. of  4 variables:
##  $ rating_id: num  1 2 3 4 5 6 7 8 9 10 ...
##  $ movie_id : num  1 1 1 1 1 3 3 3 3 3 ...
##  $ user_id  : num  1 2 3 4 5 1 2 3 4 5 ...
##  $ rating   : num  3 4 2 3 4 5 3 4 2 3 ...
ratings_df <- ratings_df %>%
  mutate(across(where(is.numeric), ~ as.integer(round(.))))
##Visual effect

library(ggplot2)
ggplot(ratings_complete, aes(x = rating)) +
  geom_bar(fill = "blue") +
  labs(title = "Distribution of Movie Ratings", x = "Rating", y = "Count")


##Attempting CSV file

write.csv(ratings_complete, "movie_ratings.csv", row.names = FALSE)
