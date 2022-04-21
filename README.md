# MLP-14
Measuring and Improving Model Fairness 

Credit Scoring Model
ML Technique used – Logistic regression (Train Set– 70%, Test Set – 30%)
Baseline model accuracy – 0.77
ROC_AUC Score – 0.71011

-------------------------------------------------------------------
Fairness Measures

On Age

a)	Anti-Classification – For each entry in the validation set, the age was randomly reassigned a number between (18,90). This model predictions on this new validation set were stored in a list y_pred_random_age (The output class predictions on the original validation set are stored in y_pred). The similarity between y_pred_random_age and y_pred are compared by taking using the sklearn’s accuracy_score(y_pred, y_pred_random_age). This tells us the percentage of entries in y_pred_random_age that match their corresponding entry in y_pred. For perfect Anti-Classification, this value should be 1. The model provides a score of 0.98 (Note: This is not deterministic since age is reassigned randomly in each run, but the score hovers around this range)

b)	Group Fairness – The validation set is split into 3 – one which has all youth (age<=24), the next which has all adults (age>24 & age<=60) and finally the last which has all the seniors(age>60). The classifier is used separately on each of these 3 sets, and the percentage of youth, adults and seniors which are classified as ‘good’ is calculated. For perfect Group Fairness, the percentage should be the same. The model classified 63.63% youth as ‘good’, 74.39% adults as ‘good’ and 80.00% seniors as ‘good’

c)	Separation – For separation, the above 3 validation sets are reused, and the correctness of the model’s prediction on each validation set is evaluated. A classification matrix for each age group is computed, from which the False Positive Rates for each group is calculated. FPR is focused on since the publisher’s of the dataset have mentioned - ‘It is worse to class a customer as good when they are bad’. The FPR for youth is 0.368, for adults is 0.461 and for seniors it is 0.0 (due to the small number of seniors in the dataset, this isn’t reliable). Note – While improving the model, both FPR and accuracy will be focused on, to ensure the FNR doesn’t become too bad.

On Gender

a)	Anti-Classification – The ‘gender’ value in each row of the validation set is flipped (from male->female and female->male). This model predictions on this new validation set were stored in a list y_pred_opposite_sex, and this is compared to the original y_pred using sklearn’s accuracy_score. The model provided a score of 0.97. (Note: Unlike the age case, this is deterministic since the flip doesn’t produce random results/is always the same everytime)

b)	Group Fairness – The validation set is split into 2 – one for males and the other with females. The classifier is used separately on these 2 sets, and the percentage of males classified as ‘good’ is compared to the percentage of females classified as ‘good’. The model classified 73.97% males as good while it classified 71.15% of females as ‘good’

c)	Separation – The above to validation sets are reused, and the model’s correctness on each set is calculated by computing the confusion matrix and comparing the FPR for males and females. The model provided an FPR of 0.382 for males and an FPR of 0.487 for females.

-------------------------------------------------------------------
Improved Fairness (Gender)

a)	Anti-Classification – To ensure anti-classifcation fairness on gender, the ‘personal_status_and_sex’ column was dropped from the dataset, and the model retrained on this new dataset. The model achieved a 1.0 anti-classification score and had an accuracy of 0.770

b)	Group-Classification – To ensure group classification, different thresholds were assigned to males and females on the original model. Males were left with their default threshold of 0.5 while females were given a more linenant threshold of 0.45. Thus, the percentage of males classified as ‘good’ remained the same at 73.97% while the percentage of females classified as good now increased to 74.03%(compared to 71.15% previously). The accuracy with these new thresholds was 0.766

c)	Separation – To ensure separation, the FPR on females (originally 0.487) was aimed to be brought down as close to FPR on males (0.382) as possible, while keeping the accuracy of the model in check. This was going to be done by finding an appropriate threshold for females (and store it in a varible called FPR_female_best_threshold). A variable named new_female_threshold would travel from 0 to 1 in intervals of 0.01. The model would make classifications on the female test set with every value of new_female_threshold. The value of new_female_threshold which provided the closest FPR value to FRP_Male (0.382) while having an accuracy(on the female validation set) within 10% to the default threshold would be stored in FPR_female_best_threshold. The final value of FPR_female _best_threshold was 0.6500 and FPR_female was now 0.384 (males is still 0.382). The accuracy of the model with the new threshold is 0.763
