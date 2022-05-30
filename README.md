# Meal-Preparation-Time
Meal preparation time was estimated given certain input features characterising the restaurants.

Meal Preparation Time Estimation
Estimating meal preparation time for its cloud kitchen brands could be an important problem. Correct predictions can help ramp up user experience on its portal. Also, this is important to estimate order delivery time to the customer. Lets explore this problem in detail.

# Business objectives & constraints
The key business objectives while developing this solution are:

User experience: A correct estimation of time significantly boosts user experience on the platform. The users can make an informed decision knowing beforehand meal preparation & delivery time. However, the cost of incorrect prediction i.e. the average deviation from the real preparation time is also important. A higher deviation will be costly in terms of adverse user experience. Thus, we can capture the deviations in form of an average root mean square error. It is easy to comprehend as its of the same unit. Most importantly it punishes higher deviation more than smaller deviation. This is relevant here because if the prediction is off by 10 mins it is more costly than being off by 5mins. Hence, RMSE can be our performance metric.

Latency: The prediction has to be in few seconds. As soon as the customer clicks on the restaurant he should be shown the preparation time of different dishes. Hence, there is a strict latency requirement.

Interpretability: Model interpretability is good to have but is not very important.

# Data analysis
The data consists of restaurant identifier, order price, number of items in the order, number of orders in queue, the time order is placed and the actual preparation time. Attached is a snapshot of the data:
<img width="638" alt="Screenshot 2022-05-24 at 8 32 21 PM" src="https://user-images.githubusercontent.com/50412680/170941784-ef579b18-b56e-4bb0-a7c4-59ff559abd37.png">Overview of the tabular data

This dataset has 30,000 orders done across 20 restaurants. Lets look at the data distribution.
<img width="868" alt="Screenshot 2022-05-24 at 8 33 30 PM" src="https://user-images.githubusercontent.com/50412680/170942031-4c7f9750-77f3-4ad9-afa3-a8c3debcb526.png">

Num. of orders for different restaurants

We see that the data isn't skewed, there are at least ~1000 orders per restaurant.
Now lets try to see as to what affects meal preparation time.
<img width="870" alt="Screenshot 2022-05-23 at 10 01 37 PM" src="https://user-images.githubusercontent.com/50412680/170941969-9df2c617-8f0d-4595-957d-5816c74002b8.png">
Median meal prep time over queued orders.
We can see that barring couple of values, meal preparation time increases with increase in queued orders. This seems logical.
<img width="751" alt="Screenshot 2022-05-24 at 8 34 59 PM" src="https://user-images.githubusercontent.com/50412680/170942106-c0df9739-6788-4f90-b431-233b34d1874c.png">

Above chart shows that the median meal preparation time varies quite a lot among the restaurants. The numeric annotations represents the number of orders handled by respective restaurant. We see that restaurant_2 although has highest orders (2663) but its median prep time is 10 mins. At the same time restaurant_4 has lowest orders (889) but has highest median prep time of around 16 minutes. This could be because of the respective restaurants key cuisines and standard operating procedure.

# Baseline Solution
Lets first evaluate if a machine learning solution is really helpful for the problem. For this we can develop a baseline solution and try to evaluate if machine learning helps improve it. We can start with median meal preparation time for each restaurant as a naive solution. On dividing the dataset between train and validation, we check performance. This gives us a root mean square error of 4.541 minutes on training and 4.459 minutes on validation dataset.
# Machine Learning
As said above we first divided our dataset in training and validation. 80% as training and 20% as validation.
training & validation data distributionAs we can see the validation data is quite representative of training data. This is very much important while evaluating performance of the system.
# Feature Engineering
Lets explore some feature engineering. We have order time and date. Therefore if the order has been done in peak hour like during lunch or dinner hours, we can expect an impact on meal preparation time, same goes for order placed on weekday compared to weekend. Therefore, these features were engineered. One hot encoding was used for categorical features.
# Random Forest
Random Forest is a bagging technique. It consists of several tree based weak learners. Together they help reduce the bias-variance trade off. The key hyperparameters for Random Forest are the number of estimators/base learners & max depth of the trees used in the bagging. Apart from these, minimum samples to split, max feature and max sample proportion were also tuned but these didn't impacted performance much. One observation was that as max depth was increased, model started overfitting. This was because each base learner was becoming more deeper. Thus, RMSE reduced for training data but not for validation data. Hence, a sweet spot was found where both the errors were minimum.
<img width="898" alt="Screenshot 2022-05-24 at 10 00 22 PM" src="https://user-images.githubusercontent.com/50412680/170942232-cba1c415-7ec4-464a-9032-0c3620a49aff.png">
Fig shows overfitting for depth > 15
Finally after tuning the relevant parameters, it resulted in the mean square error of 3.56 mins on training data and 3.95 mins for validation data. There has been an improvement of around half a minute from baseline solution. This can have a great impact when we use it at a large scale marketplace.

# Productionizing the model
This model was put on production on a small amazon web service instance (AWS) with the help of Flask.
