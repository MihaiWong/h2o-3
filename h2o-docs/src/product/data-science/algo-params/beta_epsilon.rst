``beta_epsilon``
----------------

- Available in: GLM, GAM
- Hyperparameter: no

Description
~~~~~~~~~~~

GLM and GAM include three criteria outside of ``max_iterations`` that define and check for convergence during logistic regression:

- ``beta_epsilon``: Converge if the beta change is less than this value (or if beta stops changing). This is used by solvers.
- ``gradient_epsilon``: Converge if the gradient value change is less than this value (using L-infinity norm). This is used when ``solver=L-BFGS``.
- ``objective_epsilon``: Converge if the relative objective value changes (for example, (old_val - new_val)/old_val). This is used by all solvers. 

The default for these options is based on a heurisitic:

- ``beta_epsilon``: The default for ``beta_epsilon`` is 1e-4.
- ``gradient_epsilon``: If ``lambda_search`` is set to False and lambda is equal to zero, the default value of ``gradient_epsilon`` is equal to .000001; otherwise the default value is .0001. If ``lambda_search`` is set to True, then the conditional values above are 1E-8 and 1E-6 respectively. 
- ``objective_epsilon``: If ``lambda_search=True``, then the default value of ``objective_epsilon`` is .0001. If ``lambda_search=False`` and lambda is equal to zero, then the default value of ``objective_epsilon`` is .000001. For any other value of lambda, the default value of ``objective_epsilon`` is set to .0001.

Related Parameters
~~~~~~~~~~~~~~~~~~

- `gradient_epsilon <gradient_epsilon.html>`__
- `max_iterations <max_iterations.html>`__
- `objective_epsilon <objective_epsilon.html>`__
- `solver <solver.html>`__

Example
~~~~~~~

.. tabs::
   .. code-tab:: r R

		library(h2o)
		h2o.init()

		# import the boston dataset:
		# this dataset looks at features of the boston suburbs and predicts median housing prices
		# the original dataset can be found at https://archive.ics.uci.edu/ml/datasets/Housing
		boston <- h2o.importFile("https://s3.amazonaws.com/h2o-public-test-data/smalldata/gbm_test/BostonHousing.csv")

		# set the predictor names and the response column name
		predictors <- colnames(boston)[1:13]
		# set the response column to "medv", the median value of owner-occupied homes in $1000's
		response <- "medv"

		# convert the chas column to a factor (chas = Charles River dummy variable (= 1 if tract bounds river; 0 otherwise))
		boston["chas"] <- as.factor(boston["chas"])

		# split into train and validation sets
		boston.splits <- h2o.splitFrame(data =  boston, ratios = .8)
		train <- boston.splits[[1]]
		valid <- boston.splits[[2]]

		# try using the `beta_epsilon` parameter:
		# train your model, where you specify beta_epsilon
		boston_glm <- h2o.glm(x = predictors, y = response, training_frame = train,
		                      validation_frame = valid,
		                      beta_epsilon = 1e-3)

		# print the mse for the validation data
		print(h2o.mse(boston_glm, valid=TRUE))

   .. code-tab:: python

		import h2o
		from h2o.estimators.glm import H2OGeneralizedLinearEstimator
		h2o.init()

		# import the boston dataset:
		# this dataset looks at features of the boston suburbs and predicts median housing prices
		# the original dataset can be found at https://archive.ics.uci.edu/ml/datasets/Housing
		boston = h2o.import_file("https://s3.amazonaws.com/h2o-public-test-data/smalldata/gbm_test/BostonHousing.csv")

		# set the predictor names and the response column name
		predictors = boston.columns[:-1]
		# set the response column to "medv", the median value of owner-occupied homes in $1000's
		response = "medv"

		# convert the chas column to a factor (chas = Charles River dummy variable (= 1 if tract bounds river; 0 otherwise))
		boston['chas'] = boston['chas'].asfactor()

		# split into train and validation sets
		train, valid = boston.split_frame(ratios = [.8])

		# try using the `beta_epsilon` parameter:
		# initialize the estimator then train the model
		boston_glm = H2OGeneralizedLinearEstimator(beta_epsilon = 1e-3)
		boston_glm.train(x = predictors, y = response, training_frame = train, validation_frame = valid)

		# print the mse for the validation data
		print(boston_glm.mse(valid=True))
