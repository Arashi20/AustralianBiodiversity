# AustralianBiodiversity
Project to create a model that can predict the "family" of instances in a geospatial dataset of organisms in australia, based on its coordinates.



## Introduction

My main goal is to construct a predictive neural network model designed to analyze random occurrences spanning diverse locations in Australia. The primary function of this model is to accurately assign family labels to instances, thereby providing a valuable tool for deciphering the geospatial distribution of organism families within Australia's rich biodiversity (González-Orozco et al., 2014). 
The development of this predictive model for geospatial mapping holds immense promise in advancing our understanding of ecological patterns. Through the application of machine learning techniques, the model not only aids in categorizing occurrences but also stands to significantly contribute to environmental management and conservation planning (Martinuzzi et al., 2018). This innovative approach has the potential to play a crucial role in enhancing conservation efforts by providing insights into the distribution of biodiversity across the Australian landscape.



## Data


The dataset utilized for this project originates from a citizen science initiative and was sourced from GBIF (Global Biodiversity Information Facility) [DOI: 10.15468/dl.q8bnga]. Comprising 127,628 occurrences of diverse organisms, with a predominant focus on flora and fauna, the dataset spans global locations. However, a substantial majority of the instances are situated within the rich biodiversity of Australia, providing a comprehensive foundation for the development and validation of the predictive model.

### Preprocessing

1.	I first filtered out the non-Australian occurrences with help of the “countryCode” column in the dataset.
2.	To keeping everything neat and tidy, I took a subset of the data that only contains the columns of interest (“family”, “decimalLatitude”, “decimalLongitude”, “kingdom”, “species” and “countryCode”).
3.	Since my dataset already had a lot of occurrences, I decided to check how many missing values I would find in any of the 3 columns, “decimalLatitude”, “decimalLongitude” and “family”. I noticed that having 5179 incomplete occurrences is just a small fraction of the entire dataset, so I dropped these occurrences.
4.	When checking the different counts per family classes, I realized that there would be a problem of class imbalance since the data has some big majority classes and also some minority classes (some of them even having only 1 occurrence). Based on this finding I was convinced that undersampling would be useful.
5.	When visualizing the top 10 family occurrences in Australia I found out that the distribution is mostly distributed over the coastal areas in smaller clusters – which makes sense in terms of the literature about biodiversity and the fact that a great part of Australia is covered in deserts.
6.	Finally, before proceeding to create the model I had to undersample the majority class. Based on the top 10 family counts I decided to put a threshold at 2000 and add any class counts that went over this threshold, to the majority_class.
7.	To create a sequential neural network my target variable, “family’ which I want to predict, must be numerical and not categorical. So, I used the Labelencoder from sklearn to do this.


## Model


-	Before initializing my model, I split my data into a training, validation, and test set according to the following ratio: 80/10/10. 
Model architecture:
-	With help of TensorFlow’s Keras API I created a sequential model with 5 dense layers along with the implicit input layer, making it a 6-layer neural network. Four of the dense layers make use of a “ReLu” activation function, which introduces non-linearity and facilitates the learning of complex patterns within the data. The final dense layer is the output which has a “softmax” activation function for multiclass classification (1719 different classes in this case). 
-	As for the loss function I picked a sparse categorical cross-entropy loss function, since this one is used a lot in classification tasks where classes are mutually exclusive (which is the case for this data where I used a Labelencoder for the target variable). 
Evaluation
-	I Initially trained the model for 50 epochs with a batch size of 50, which yielded suboptimal results (15-20% accuracy). Subsequently, the model was retrained for 300 epochs with a batch size of 60, resulting in improved accuracies of approximately 33%.
-	The weighted F1-score, a holistic metric considering precision and recall, was calculated, aligning closely with the overall accuracies (~31%).
-	Delving into the classification report, notable variations in the weighted F1-scores were observed across different classes. "Anatidae" and "Myrtaceae" exhibited the highest F1-scores (0.47), while "Meliphagidae" recorded the lowest (0.15). This highlights the model's diverse performance concerning specific family labels.
-	This makes sense since both “Anatidae” and “Myrtaceae” are part of the top 10 family occurrences in the data(see "data visualisation" in the ipynb file).




## Discussion


In conclusion, the evaluation of the predictive model for geospatial mapping of biodiversity in Australia reveals both achievements and areas for further enhancement. The iterative process of model tuning played a pivotal role in improving overall accuracy, transitioning from an initial 15-20% to approximately 33% after retraining for 300 epochs with a batch size of 60. This adjustment demonstrates the model's capacity to learn and capture complex patterns within the data, leading to more reliable predictions.
The application of the weighted F1-score provided a comprehensive assessment of model performance, aligning closely with the overall accuracies at approximately 31%. The consideration of precision and recall collectively in the F1-score contributes to a nuanced understanding of the model's effectiveness in handling various class imbalances and predicting outcomes across diverse family labels.
While the achieved accuracies and F1-scores represent significant progress, it is imperative to acknowledge the ongoing nature of model refinement. Future iterations may focus on addressing specific challenges observed in class-specific performance, ensuring a more balanced and precise prediction across a broader spectrum of family labels. Overall, this model holds promise as a valuable tool for geospatial mapping of biodiversity, with continuous improvement serving as a key aspect of its evolution.
One drawback of a model like this could be that Relying solely on quantitative model outputs might lead to an overemphasis on numeric metrics, potentially neglecting qualitative dimensions of biodiversity conservation.
Moreover, The use of predictive models in conservation raises ethical considerations. Decisions based solely on model predictions might overlook qualitative aspects of conservation, such as cultural significance or the uniqueness of certain ecosystems.


## Limitations



The primary challenge in this project was the imbalanced nature of the dataset. The presence of majority classes and several minority classes with only one occurrence each posed difficulties in achieving a well-balanced model. The imbalanced distribution may have led to biased predictions, where the model tended to favor the majority classes. While efforts were made to address this imbalance using techniques like undersampling, the limitations persisted, especially for classes with minimal representation.



The application of SMOTE, a common technique to address class imbalance, faced limitations in the context of this project. Classes with only one occurrence could not benefit significantly from synthetic sample generation, as SMOTE relies on the availability of multiple instances within a class for meaningful synthesis. Alternative strategies, such as manual adjustments or grouping of sparse classes, might be explored in future iterations.


## References


González-Orozco, C. E., Ebach, M. C., Laffan, S., Thornhill, A. H., Knerr, N. J., Schmidt-Lebuhn, A. N., Cargill, C. C., Clements, M., Nagalingum, N. S., Mishler, B. D., & Miller, J. T. (2014). Quantifying Phytogeographical Regions of Australia Using Geospatial Turnover in Species Composition. PLOS ONE, 9(3), e92558. https://doi.org/10.1371/journal.pone.0092558
GBIF.org (18 June 2018) GBIF Occurrence Download  https://doi.org/10.15468/dl.pkuedr
MARTINUZZI, S., RIVERA, L., POLITI, N., BATEMAN, B., RUIZ DE LOS LLANOS, E., LIZARRAGA, L., RADELOFF, V. (2018). Enhancing biodiversity conservation in existing land-use plans with widely available datasets and spatial analysis techniques. Environmental Conservation, 45(3), 252-260. doi:10.1017/S0376892917000455
