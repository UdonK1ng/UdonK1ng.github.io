# <span style="color: #004a99;">Background</span>
<span style="font-size: larger;">Perovskite cells can potentially be the next generation of solar cells that have potential to be more effective than traditional and widely used silicon cells.  One drawback to this is they tend to degrade at a much faster rate than the current silicon cells when under direct sunlight. 
  Dr. Fenning's lab employs an experimental method involving various samples of perovskite
film using different synthesis conditions and testing them under accelerated conditions –
intense sunlight and high temperatures – to track degradation speed of these films. The
challenge is that these experiments can last for weeks to months before conclusions are
clear. A potential solution is to train a machine-learning model on past
experiments so we can learn which factors significantly impact film degradation and how
to optimize cell production for longer durability. If we can figure out what factors lead to longer durability, we can allow Dr. Fenning's lab to create a better perovskite solar cell which can potentially spread to widespread commercial use! Our project first created a pipeline that puts any new data daily into the graph database so it can be explored further, and another pipeline that performs the machine learning on the data from the graph database. </span>
 

# <span style="color: #004a99;">Data Description</span>
  Our current data set currently contains 147 samples. Each sample includes different characteristics from the experiment like chemical compositions, manufacturing steps, solutes, and solvents. The data is extracted from a neo4j graph database into a table so we can do analysis on the data and find which characteristics are important.  
  
Here is an example of one sample from our graph database:


![pasted image 0 (2)](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/9fa5549f-6419-4dc9-9582-35b792f3efb9)
Here is an example of the chemical node and its properties in the graph database:

![pasted image 0 (1)](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/073ef04a-34ed-4137-be0b-0addd3355dbe)


Here is an example of the colormetrics node and its properties in the graph database: 

![pasted image 0](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/f417559e-e01c-4b51-a13a-c5b6d03495dc)
# <span style="color: #004a99;">Methods</span>
Image Processing:
  We are measuring the color change from black to yellow, where yellow is completely degraded. Images were taken every couple of minutes over the course of the experiment, where films are placed under intense sunlight and temperature conditions. Here is a timelapse of the degredation process:

![TimelapseofCellDegredation-ezgif com-video-to-gif-converter](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/15047423-51cc-44fa-8b07-94b8548a2eba)



We analyze the color of the film and fit the degredation value on a logistic curve. This is known as our colormetrics pipeline. We are fitting for both x0 and k parameters where, the x0 parameter is the midpoint of the degredation curve and the k parameter is the rate of change of the curve.  Here is the sampled colormetrics graph:

![example_cmet_graph](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/4ec45804-10f7-4d5d-a0dd-8fe53979a4ca)

Here is the MSE distribution for each fitted curve compared to the actual curve:
![mse_distribution](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/95394fef-d858-45ef-afdf-49c76b0e976b)

Graph Database Pipeline:
  Data is fetched daily from Synology NAS system and automatically updates the neo4j graph database. From the graph database, we use the package py2neo to work on data from the graph database in a jupiter notebook and tabularize the data, so we can build a model to fit the data.  


Machine Learning Pipeline: 
  Due to our limited dataset, we tested models that were generally used for smaller datasets which included: Ridge Regression, Lasso Regression, Elastic Net, Random Forest Regressor, Support Vector Regressor, and Catboost.  We also tested hyperparameters for every model except for Catboost.
  Here is a flowchart depicting the entire process starting from the data all the way to the creation of the machine learning model. 
![Project Flow chart (3)](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/abefca97-76bc-4e5e-b4d8-1ff3031a20db)
# <span style="color: #004a99;">Results</span>
  To judge model performance, we first did an 80/20 train test split on the data, which is essentially where the model learns patterns based on the 80% of the data and then predicts values on our logistic curve for the other 20%.  We used cross validation to split the data into 80/20 multiple times depending on the number of folds.  We also calculated the RMSE(Root Mean Squared Error) for the x0 and the k parameters which is how much off the actual values are compared to what the model predicted the value would be. Here are our graphs showing the RMSE for different models as sample size increases.  The same color bands show the 95% confidence interval that comes from testing the model 100 times, while the solid line is the average RMSE from all the tests. 

![x0_sample_performance (1)](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/a26fb123-43b5-4115-83e6-1e0dee7d18d7)

The figure above is performance on x0 parameter
![k_sample_performance (1)](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/471d2b07-2968-4a84-8aa6-6c3d686717f7)

The figure above is performance on k parameter.

Catboost performed the best in our tests for both parameters. Since catboost performed the best in our models, we used catboost to determine which features caused a change in the degredation rate. Here are the relative importances for features below:
![combined_feature_importance (1)](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/71a6fed9-e096-4118-8294-cd2321228c10)
![combined_feature_importance](https://github.com/UdonK1ng/DSC180AB-Solar/assets/97561013/f8838c86-2bd8-46c9-94bf-2f837c5cad36)

# <span style="color: #004a99;">Conclusion</span>
  From the figures above, we can see that our models naturally performed better as more samples were added to the graph database. Catboost appeared to have the lowest RMSE across all sample levels and both x0 and k parameters. 
  Future work involves adding more data to our database in order to make our machine learning models more accurate. We also want to figure out whether parameters x0 and k are independent or can a model be built that predicts both parameters, as there seems to be a negative relationship between x0 and k. Eventually, it may be possible to automate the colormetric pipeline for every new batch that comes in, and even the entire process eventually so putting data into the synology data base will automatically tell you the best model and which features are important!



  
# <span style="color: #004a99;">Acknolwedgements</span>
Special thanks to Prof David Fenning and SOLEIL member Deniz Cakan for resources and guidance on our project!



# <span style="color: #004a99;">Links</span>

[GitHub Repository](https://github.com/ajohari114/dsc180-solar2.git)

[Report](https://github.com/omicron3808/artifact-directory/blob/main/report.pdf)

