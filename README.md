# Athens-Airbnb-Invest-Location-Amenities-Revenue-Forecast
I am thinking about starting an Airbnb business in Athens - either buying or renting an apartment to host on the platform. Since I am not familiar with the city, I would like to know 
1.Which neighborhoods should I consider? 
2.What equipment and furnishings will maximize my occupancy? 
3. How can I estimate the expected revenue for a new listing?
For all three questions we'll use Airbnb data, which contains the necessary information.
A quick look shows 15,584 rows and 79 columns. Each row represents a listing and includes attributes such as neighborhood, price, ratings, amenities, availability, host details, description, reviews, and more.
Question 1: Which neighborhoods should I consider buying or renting in?
Since my goal is to buy or rent an apartment, the first step is to filter the dataset to listings where room_type equals "Apartment" (or the exact Airbnb value, e.g. "Entire home/apt").
>> df = df[df["room_type"] == "Entire home/apt"]
After filtering for apartments, I ended up with 14414 listings. I then plotted the distribution of listings by neighborhood to show where most apartments are located.
It looks like "emporiko trigono plaka" has the most apartments, but that doesn't tell us whether they are actually being rented. A simple way to estimate bookings over the last year is to derive booked_days from the availability_365 field:
>> df['booked_days'] = 365 - df['availability_365']
I'm also interested in whether booked_days correlates with other variables (bathrooms, bedrooms, rating, etc.).
After checking the data, there's no meaningful correlation between booked_days and the other variables (bathrooms, bedrooms, price, ratings, etc.). Therefore, we'll proceed with descriptive visualizations to explore patterns, distributions and segment-level differences.
High listing counts in a neighborhood don't necessarily mean high occupancy. To get a clearer picture, we should compare average booked days per neighborhood and add average Location and Value ratings for context.
Surprisingly, the most rented apartments are not located in the area with the highest number of listings.
Where should I buy or rent? I would say select neighboorhoods that are having high avg booking days and over 4.7 value for money and location rating for exmp "1Ο ΝΕΚΡΟΤΑΦΕΙΟ", "ΑΝΩ ΠΑΤΗΣΙΑ", "ΣΤΑΔΙΟ", and "ΛΥΚΑΒΗΤΤΟΣ". 
Question 2: What equipment and furnishings will maximize my occupancy and bookings?
For the second question, we'll identify which equipment and amenities an apartment should include. First, we'll create a word cloud from the amenities data to highlight the most common features. As we want to have a good running business I will analyze only the apartments that was geeting more tha 4.5 overall rating.
>>df1['review_scores_value'] = pd.to_numeric(df1['review_scores_value'], errors='coerce')
>>df1 = df1[df1['review_scores_value'] >= 4.5]
We will build a word map with matching patterns to get a better overview. As we see also below a must have list for our apartment are:
('wifi', 11219), ('air_conditioning', 11190), ('hair_dryer', 10385), ('kitchen', 10275), ('hot_water', 10117), ('hangers', 9913), ('iron', 9862), ('heating', 9846), ('dishes_and_silverware', 9784), ('cooking_basics', 9494), ('bed_linens', 9378), ('essentials', 8847), ('shampoo', 8543), ('refrigerator', 8506), ('parking', 8306), ('washer', 8231), ('hot_water_kettle', 8017), ('self_check_in', 7751)
Question 3: How can I estimate the expected revenue for a new listing?
For the third question, I selected four neighbourhoods to consider for buying or renting an apartment: "1Ο ΝΕΚΡΟΤΑΦΕΙΟ", "ΑΝΩ ΠΑΤΗΣΙΑ", "ΣΤΑΔΙΟ", and "ΛΥΚΑΒΗΤΤΟΣ". I included price as a key feature for the revenue prediction, but a quick data check showed that more than 900 listings have missing prices (NaN). To address this, I plan to impute those missing values using a group-based approach e.g., replacing NaNs with the mean price within each neighborhood to preserve local price structure.
>> grouped = (df2.groupby(['neighbourhood_cleansed', 'accommodates'], as_index=False).agg(avg_price=('price_clean', 'mean'), n_listings=('price_clean', 'size')))grouped['avg_price'] = grouped['avg_price'].round(2)
>> grouped = grouped.sort_values(['neighbourhood_cleansed', 'accommodates']).reset_index(drop=True)
After imputing missing prices and preparing all necessary features, we can estimate annual revenue per listing. Using those variables, I will train a model to predict expected yearly revenue for a new apartment, taking into account both its characteristics and the target neighbourhoods.
I plan to use a Random Forest regressor for the revenue prediction because it handles nonlinearity and mixed feature types well. I split the dataset (e.g., 80% train / 20% test) to evaluate generalization on held-out data. Because revenue is typically right‑skewed, I apply a log1p transformation to stabilize variance and make the regression problem easier for the model.
For evaluation, I computed MAE, RMSE, and R² on the test set after back‑transforming the predictions to the original scale, so the metrics reflect absolute error, root-mean-square error, and explained variance in euros. After running the model, I built an interactive prediction example where you can easily change all features for an apartment of interest and obtain the model's predicted annual revenue.
Example
Conclusion
In summary, we now know which neighborhoods to target when searching for an apartment, which amenities to invest in to make it more attractive to guests, and how to estimate the expected annual revenue. This project provides a high-level overview and does not include detailed cost calculations for the apartment. Please feel free to explore further on your own and let me know your thoughts.
