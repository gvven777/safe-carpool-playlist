# safe-carpool-playlist
Safe Carpool Playlist Recommender

My friends and I listen to different genres, but pop and hip-hop are what most of us have in common. On a car ride, it's hard to pick songs everyone knows and enjoys. I built this recommender to help: give it a few songs the group already likes, and it returns pop and hip-hop songs with a similar vibe. It only searches popular, upbeat tracks with vocals, so it works as a starting point for a shared playlist.

Built with Python (pandas, NumPy, scikit-learn, matplotlib).

How it works
1. Clean the data. Combine the high- and low-popularity files, drop duplicate tracks (by track_id), and drop rows with missing audio features.
2. Define "carpool-safe." Turn a vague idea into measurable rules on Spotify audio features (each scored 0 to 1, except popularity):
- popularity >= 50 (a proxy for "songs people have probably heard")
- danceability >= 0.7
- energy between 0.5 and 0.9
- valence (mood) >= 0.4
- acousticness <= 0.4
- instrumentalness <= 0.1 (has vocals to sing along to)
- speechiness <= 0.3 (more singing than talking)
  
3. Build a weighted feature space. Standardize danceability, energy, valence, acousticness, speechiness, tempo, and loudness, then weight them. Speechiness gets the highest weight because it best separates hip-hop from pop.
  
4. Recommend from seed songs. Average the feature vectors of the seed songs and return the songs closest to that average (Euclidean distance). Seeds are meant to be songs the group likes, such as one favorite from each friend.

python
seeds = [9, 149, 1330, 179, 150]   # row indexes of favorite songs
recommend_from_seeds(seeds, ["hip-hop", "pop"])

Data
Source: Spotify Music Dataset on Kaggle. It is not included in this repo, so download it and place the two CSV files next to the notebook.
4,831 rows before cleaning (1,686 high-popularity and 3,145 low-popularity), and 4,494 after removing duplicates and missing values.
515 songs pass the carpool-safe filter. Within hip-hop and pop, there are 811 songs, and 170 are carpool-safe (95 pop, 75 hip-hop).

Exploratory analysis (hip-hop vs. pop)
Pop reaches higher popularity scores than hip-hop and also includes many low-popularity tracks. Hip-hop is concentrated between roughly 55 and 85.
Speechiness separates the genres clearly: the hip-hop median is about 0.22, versus about 0.04 for pop.
Among carpool-safe songs, hip-hop and pop overlap heavily on energy and danceability, so those two features alone can't tell the genres apart.
Energy and loudness are the most strongly correlated features, and energy is negatively correlated with acousticness.

Evaluation
Genre was not an input to the model, so I used it as a sanity check. For every hip-hop or pop song, I checked how many of its 5 nearest neighbors share its genre.

Method	Genre match
This recommender	0.792
Random picks (baseline)	0.514

I also listened to the recommendations for a set of seed songs and judged them by ear: they were songs I'd play in a car with friends. That check was informal and small.

What I changed along the way
My first version used cosine similarity on min-max scaled features. Scores were nearly identical across songs, so I switched to standardized features with Euclidean distance and manual weights.
Songs with matching tempo and energy but a different groove (house vs. trap, for example) were ranked as similar. I raised the speechiness weight and added the option to restrict results by genre.

Limitations
"Safe" means popular, upbeat, and vocal-driven. The dataset has no explicit column, so the filter cannot screen for explicit lyrics.
Popularity is only a stand-in for songs people know.
Genre labels come from playlists, not from how the songs sound, so some labels are loose.
Spotify's audio features don't capture rhythmic feel or bass style.
With two genres, the random baseline is already about 0.5, so the genre-match test is a rough check. The listening test was informal.
The weights and thresholds are my own judgment calls.

Run it
pip install pandas numpy scikit-learn matplotlib jupyter
jupyter notebook

Open the notebook and run the cells in order.
