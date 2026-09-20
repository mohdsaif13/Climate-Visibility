# Climate Visibility Prediction

A machine learning project for estimating **visibility distance from weather conditions**. The project takes weather observations, runs them through a training pipeline, and exposes a simple web interface for generating visibility predictions.

## What this project does

Poor visibility can affect aviation, road transportation, and other outdoor operations. The idea behind this project is to use historical weather data to learn the relationship between atmospheric conditions and measured visibility.

The repository includes the complete flow rather than only a trained model:

**Data → Validation → Transformation → Model Selection → Hyperparameter Tuning → Prediction → Web App**

## Main features

- Weather data ingestion with MongoDB support
- Data validation before model training
- Feature scaling using `StandardScaler`
- Comparison of multiple regression algorithms
- Hyperparameter tuning with `GridSearchCV`
- Model serialization and artifact management
- AWS S3 support for storing and retrieving trained models
- Flask-based interface for training and prediction
- Docker support for packaging the application
- Project structure separated into components, configuration, pipeline, utilities, logging, and exceptions

## Weather features used

The project schema works with weather observations such as:

- Dry-bulb temperature
- Relative humidity
- Wind speed
- Wind direction
- Station pressure
- Sea-level pressure
- Precipitation
- Visibility (target)

The current schema removes a few fields before training, including `DATE`, `WETBULBTEMPF`, `DewPointTempF`, `StationPressure`, and `Precip`.

## Model training

The training component compares several regression models:

- Linear Regression
- Ridge Regression
- Lasso Regression
- Random Forest Regression
- Gradient Boosting Regression

The best-performing model is selected using **R² score** on the validation split. The selected model is then tuned with `GridSearchCV` using the parameter ranges defined in `config/model.yaml`.

The trained estimator is wrapped together with the preprocessing object so the same transformation can be used during prediction.

## Project pipeline

### 1. Data Ingestion
Weather data is collected and prepared through the data ingestion component. MongoDB is supported as the data source.

### 2. Data Validation
Incoming data is checked against the project schema before moving forward in the pipeline.

### 3. Data Transformation
The validated data is split into training and testing sets. Numerical inputs are standardized with `StandardScaler`, and the fitted preprocessing object is saved as an artifact.

### 4. Model Training
Several regression models are tested. The model configuration in `config/model.yaml` controls the hyperparameter search.

### 5. Model Evaluation
The final model is evaluated using the R² metric. The project also keeps the model score as part of the training flow.

### 6. Model Storage
The trained model can be synchronized with an AWS S3 bucket through the project's S3 utility.

### 7. Prediction
The prediction pipeline downloads the stored model when needed, loads it, applies the saved preprocessing step, and returns the predicted visibility value.

## Web application

The project includes a Flask application in `app.py`.

Available routes:

- `/` — home page
- `/train` — starts the training pipeline
- `/predict` — accepts prediction input and displays the result

The application is configured to run on port **8062**.

## Tech stack

**Languages & libraries**

- Python
- Pandas
- NumPy
- Scikit-learn
- XGBoost
- Matplotlib
- Seaborn
- FastAPI / Flask components

**Data & cloud**

- MongoDB / MongoDB Atlas
- AWS S3
- Azure

**Deployment & tooling**

- Docker
- GitHub Actions
- YAML-based configuration

## Project structure

```text
Climate-Visibility/
│
├── app.py
├── Dockerfile
├── requirements.txt
├── model.pkl
│
├── config/
│   ├── model.yaml
│   ├── prediction_schema.yaml
│   └── schema.yaml
│
├── notebooks/
│   ├── EDA.ipynb
│   ├── EDA_raw_data.ipynb
│   ├── Feature_engineering and model training.ipynb
│   └── mongodbupload.ipynb
│
├── src/
│   ├── cloud_storage/
│   ├── components/
│   ├── configuration/
│   ├── data_access/
│   ├── exception/
│   ├── ml/
│   ├── pipeline/
│   └── utils/
│
├── artifacts/
├── Results/
├── static/
├── templates/
└── uploaded_data/
```

## Running the project locally

### Clone

```bash
git clone https://github.com/mohdsaif13/Climate-Visibility.git
cd Climate-Visibility
```

### Create an environment

The original project setup uses a Conda environment:

```bash
conda create --prefix venv python=3.7 -y
conda activate venv/
```

### Install dependencies

```bash
pip install -r requirements.txt
```

### Configure environment variables

Set the required AWS and MongoDB values in your environment:

```bash
export AWS_ACCESS_KEY_ID=<AWS_ACCESS_KEY_ID>
export AWS_SECRET_ACCESS_KEY=<AWS_SECRET_ACCESS_KEY>
export AWS_DEFAULT_REGION=<AWS_DEFAULT_REGION>
export MONGODB_URL=<MONGODB_URL>
```

### Start the application

```bash
python app.py
```

Then open:

```text
http://localhost:8062/
```

## Docker

Build the image:

```bash
docker build -t climate-visibility .
```

Run it:

```bash
docker run -d -p 8062:8062 climate-visibility
```

## Results

The repository includes UI screenshots in the `Results` directory showing the application input and prediction output pages.

<p align="center">
  <img src="https://raw.githubusercontent.com/mohdsaif13/Climate-Visibility/main/Results/UI.JPG" alt="Climate Visibility prediction interface" width="820">
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/mohdsaif13/Climate-Visibility/main/Results/Output%20UI.JPG" alt="Climate Visibility prediction output" width="820">
</p>

## Why I built this

I wanted to work through the full machine learning lifecycle on a real weather dataset instead of stopping after model training. This project gave me hands-on practice with data validation, preprocessing, model comparison, hyperparameter tuning, model packaging, cloud storage, and serving predictions through a web application.

## Project notes

This repository is a practical project and the current implementation reflects the code and configuration included here. Cloud services such as MongoDB Atlas, AWS S3, and Azure require the appropriate credentials and setup before the complete pipeline can be run.

## Author

**Md Saif Ali**

Data Science | Machine Learning | AI/ML

[GitHub](https://github.com/mohdsaif13) · [LinkedIn](https://www.linkedin.com/in/md-saif-ali-a3250825b/)
