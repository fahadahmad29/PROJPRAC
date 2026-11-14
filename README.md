# Network Security Phishing Detection

A machine-learning application for detecting phishing-related network data. The project includes a modular training pipeline, MongoDB-based data ingestion, model artifact generation, and a FastAPI service for training and CSV predictions.

## Features

- Data ingestion from MongoDB or local project data.
- Data validation and transformation before training.
- Scikit-learn model training with persisted preprocessing and model artifacts.
- FastAPI endpoints for model training and batch prediction.
- CSV prediction results rendered as an HTML table.
- Docker support for running the API in a container.
- Optional AWS ECR/EC2 deployment workflow. 

## Project Structure

```text
.
├── app.py                 # FastAPI application
├── main.py                # Command-line training pipeline
├── push_data.py           # Import CSV records into MongoDB
├── networksecurity/       # Pipeline components, entities, utilities, and constants
├── Network_Data/          # Input data directory
├── data_schema/           # Data schema files
├── final_model/           # Saved preprocessor and model artifacts
├── prediction_output/     # Generated prediction CSV files
├── templates/             # HTML templates used by the API
├── Dockerfile
├── requirements.txt
└── setup.py
```

## Requirements

- Python 3.10 or later
- MongoDB connection string when using MongoDB ingestion or the API
- Git
- Docker (optional)

## Local Setup

1. Clone the repository and enter the project directory:

   ```bash
   git clone https://github.com/fahadahmad29/PROJPRAC.git
   cd PROJPRAC
   ```

2. Create and activate a virtual environment:

   ```bash
   python -m venv .venv
   source .venv/bin/activate       # Linux/macOS
   # .venv\\Scripts\\activate    # Windows
   ```

3. Install dependencies:

   ```bash
   pip install -r requirements.txt
   pip install -e .
   ```

4. Create a `.env` file in the project root. Do not commit this file:

   ```env
   MONGODB_URL_KEY=<your-mongodb-connection-string>
   MONGO_DB_URL=<your-mongodb-connection-string>
   ```

   `MONGODB_URL_KEY` is used by `app.py`. `MONGO_DB_URL` is used by `push_data.py`.

## Training the Model

Run the end-to-end training pipeline:

```bash
python main.py
```

The pipeline performs data ingestion, validation, transformation, and model training. The generated artifacts are stored under `final_model/`.

To trigger training through the API, start the application and open:

```text
GET /train
```

## Running the API

Start the FastAPI application with:

```bash
python app.py
```

The service runs on `http://localhost:8000`.

Useful URLs:

- Interactive Swagger documentation: `http://localhost:8000/docs`
- ReDoc documentation: `http://localhost:8000/redoc`
- Training endpoint: `GET /train`
- Prediction endpoint: `POST /predict`

### Making a Prediction

Send a CSV file to the `/predict` endpoint. The CSV should contain the feature columns expected by the trained preprocessing pipeline.

Example using cURL:

```bash
curl -X POST "http://localhost:8000/predict" \\
  -H "accept: text/html" \\
  -F "file=@path/to/input.csv"
```

The response contains an HTML table with a `predicted_column` field. A copy of the output is also written to `prediction_output/output.csv`.

## Loading Data into MongoDB

To convert a CSV file to records and insert them into MongoDB, configure `MONGO_DB_URL` in `.env`, update the database and collection values in `push_data.py` if needed, and run:

```bash
python push_data.py
```

## Docker

Build and run the API container:

```bash
docker build -t network-security .
docker run --env-file .env -p 8000:8000 network-security
```

Then visit `http://localhost:8000/docs`.

## AWS Deployment Notes

The project can be deployed to an EC2 instance using Docker and an Amazon ECR repository. Configure AWS credentials through your deployment environment or GitHub Actions secrets rather than committing them to the repository.

Typical GitHub Actions variables include:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_REGION
AWS_ECR_LOGIN_URI
ECR_REPOSITORY_NAME
```

Never expose access keys, MongoDB credentials, or other secrets in source code, images, logs, or committed configuration files.

## License

No license has been specified yet. Add a license before distributing or reusing this project publicly.
