✈️ Twitter US Airline Sentiment Analysis Report
1. Problem Framing 
The goal of this project is to transform raw customer feedback from Twitter into actionable business intelligence for airline customer support and operations teams.
Business Question
"Which specific, actionable issues (topics) mentioned in negative tweets are the primary drivers of low customer sentiment, and how should our customer support team prioritize responding to these issues to maximize customer satisfaction?"
This question ensures the analysis is focused on negative feedback and results in a prioritized, resource-allocation strategy.
Success Metric
Reduction in the volume of negative sentiment tweets related to the top 3 identified actionable topics by 15% within three months of implementing the new support prioritization strategy.
This metric is quantifiable, time-bound, and directly measures the business impact of the project's recommendations.
________________________________________
2. Data Import & Exploratory Data Analysis 
The project began by loading the Tweets.csv dataset, which contains over 14,000 tweets related to US airlines.
Key EDA Insights:
•	Focus: The analysis primarily focused on the negative tweets, which comprise the majority of the dataset.
•	Pre-Labeled Reasons: The negativereason column provided an initial view of the problem landscape, showing that issues like "Customer Service Issue" and "Late Flight" are the most frequent causes of complaints.
•	Data Integrity: The data was generally clean, but tweets needed noise reduction (mentions, URLs, symbols) before topic modeling could be effective.
________________________________________
3. Text Preprocessing (15%)
Effective topic modeling requires standardizing the text data by reducing noise and linguistic variations. The following steps were applied to the text column:
1.	Lowercasing: Standardized all text to prevent the model from treating 'Flight' and 'flight' as different words.
2.	Noise Removal: Removed Twitter handles (@mentions), URLs, and non-alphabetic characters.
3.	Stop Word Removal: Eliminated common, non-descriptive words (e.g., 'the', 'a', 'is') using the NLTK stop list.
4.	Lemmatization: Reduced words to their root form (e.g., 'running' became 'run', 'delays' became 'delay') using the WordNet Lemmatizer to group related terms.
This process created the cleaned_text column, which served as the input for all subsequent modeling.
________________________________________
4. Topic Modeling 
Goal: Discover the underlying themes driving customer complaints that were not explicitly categorized by the original data collectors.
Algorithm Choice Justification: NMF
We chose Non-Negative Matrix Factorization (NMF) over Latent Dirichlet Allocation (LDA) because:
•	Conciseness: NMF is superior for short, distinct documents like tweets, where one tweet usually belongs to one clear topic.
•	Interpretability: NMF tends to produce more distinct and cohesive word clusters, leading to highly actionable and easy-to-label topics (e.g., 'check baggage fee', 'website mobile app').
Output (Example Topics)
The NMF model was run to identify 8 distinct topics. Below is an example of the kind of output generated:
Topic ID	Top 5 Keywords	Actionable Theme
Topic 1	flight, hour, late, delay, min	Severe Delay/Late Flight
Topic 4	service, issue, bad, phone, customer	Poor Customer Service
Topic 6	ticket, help, booking, email, change	Booking/Ticket Issues
________________________________________
5. Sentiment Analysis using LLMs (15%)
Goal: Go beyond the categorical 'negative' label to measure the intensity or severity of the complaint using advanced language models.
Methodology (LLM Integration)
Instead of relying on simple rule-based models (like VADER), the project is designed to use a large language model (LLM), such as Groq, via API calls.
1.	A dedicated function, get_llm_sentiment(), is established with an API key placeholder.
2.	The LLM is prompted to score the tweet on a continuous scale (e.g., -1.0 to 1.0), providing a highly nuanced llm_sentiment_score.
Analysis
By averaging the llm_sentiment_score for all tweets belonging to each NMF topic (from Part 4), we identify the most severe issues. The topics with the lowest average score (closest to -1.0) are the highest priority for immediate action, as they cause the most customer distress.
________________________________________
6. Content-Based Recommender 
Goal: Build a system to assist customer support agents by instantly finding past, similar tweets and their resolution paths.
Methodology: Cosine Similarity
1.	TF-IDF Matrix: A final Document-Term Matrix was created from all tweets using the TF-IDF Vectorizer (including bigrams for better context).
2.	Similarity Calculation: The Cosine Similarity metric was calculated across the entire matrix. Cosine Similarity measures the angle between two tweet vectors; a score close to 1.0 means the tweets are almost identical in content.
3.	Recommendation: Given any new incoming tweet (query), the system finds the top 10 tweets with the highest Cosine Similarity scores.
Value Proposition
When an agent receives a tweet about "a flight delay due to maintenance," the recommender instantly surfaces 10 previous tweets with the same issue, allowing the agent to provide a consistent, informed, and rapid response.
________________________________________
7. Communication, Reproducibility & Presentation 
Reproducibility
•	Fixed Random Seeds: All stochastic processes (NMF training, NumPy random sampling) were initialized with np.random.seed(42) to ensure the exact same results upon every run.
•	Parameter Documentation: Key parameters (e.g., N_TOPICS = 8, max_df=0.95) were defined and documented clearly within the code.
•	Top-to-Bottom Execution: The entire notebook is structured to run sequentially without manual intervention.
Communication & Presentation
The report uses clear figures (bar charts showing severity by topic, distribution of reasons) and a concise narrative focused on the actionability of the findings. The final output is the prioritized list of NMF topics, ranked by LLM Sentiment Severity, directly informing the recommended customer support strategy.
