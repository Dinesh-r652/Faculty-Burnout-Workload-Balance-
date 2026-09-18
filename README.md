# Faculty Burnout & Workload Balancer

## Project Overview

The **Faculty Burnout & Workload Balancer** is an AI/ML-based system designed to analyze faculty workload and predict burnout risk based on teaching, advising, committee, research, administrative responsibilities, semester progress, and historical leave data.

The project combines **Machine Learning, Flask REST API, Redis Caching, and RabbitMQ Messaging** into an end-to-end application.

---

## Project Objective

- Analyze faculty workload distribution.
- Predict faculty burnout risk using Machine Learning.
- Provide predictions through a REST API.
- Reduce repeated ML predictions using Redis caching.
- Publish prediction events through RabbitMQ.
- Consume and process prediction messages.
- Integrate all components into one end-to-end workflow.

---

## System Architecture

```text
Faculty Data
     |
     v
Random Forest ML Model
     |
     v
Flask REST API
     |
     v
Check Redis Cache
   /       \
HIT       MISS
 |          |
 |          v
 |     ML Prediction
 |          |
 |          v
 |     Store in Redis
 |          |
 |          v
 |       RabbitMQ
 |          |
 |          v
 |       Consumer
 |          |
 |          v
 |   Message Processing
 |
 +-------> API Response
```

---

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Main programming language |
| Pandas | Data processing |
| NumPy | Numerical operations |
| Scikit-learn | Machine Learning |
| Random Forest | Burnout risk classification |
| Joblib | Model saving/loading |
| Flask | REST API |
| Redis | Caching prediction results |
| Pika | Python RabbitMQ client |
| RabbitMQ | Message queue |
| JSON | API and message data format |
| Google Colab | Development and testing |

---

# Phase 1 - Dataset & Machine Learning

## Dataset

A synthetic dataset containing **500 faculty records** was generated.

### Features

| Feature | Description |
|---|---|
| `Faculty_ID` | Unique faculty identifier |
| `Teaching_Hours` | Weekly teaching workload |
| `Advising_Students` | Number of students advised |
| `Committee_Count` | Number of committees handled |
| `Research_Hours` | Research workload |
| `Admin_Hours` | Administrative workload |
| `Semester_Progress` | Current semester progress |
| `Historical_Leave_Days` | Previous leave days |
| `Burnout_Risk` | Target classification |

### Burnout Risk Classes

```text
Low
Medium
High
```

### Completed Work

- Generated 500 faculty records.
- Calculated workload score.
- Calculated pressure score.
- Created burnout-risk labels.
- Performed data preprocessing.
- Handled missing values.
- Applied feature scaling.
- Created train/test split.
- Trained Random Forest classifier.
- Saved the trained ML pipeline.

### Random Forest Configuration

```text
n_estimators = 150
max_depth = 8
random_state = 42
class_weight = balanced
```

### Saved Model

```text
faculty_burnout_model_pipeline.pkl
```

### Phase 1 Status

```text
COMPLETED
```

---

# Phase 2 - Model Testing & Flask REST API

## Completed Work

### Model Testing

The trained model was loaded and tested with faculty workload data.

The model generates:

- Predicted burnout risk.
- Risk probabilities.

Example:

```json
{
    "predicted_burnout_risk": "Low",
    "risk_probabilities": {
        "High": 0.0,
        "Low": 0.7146,
        "Medium": 0.2854
    }
}
```

### Flask REST API

Created a REST API endpoint:

```text
POST /predict
```

The API accepts faculty workload information in JSON format and returns the burnout prediction.

### API Response

```text
Faculty_ID
Predicted Burnout Risk
Risk Probabilities
```

### API Testing

The API was successfully tested with:

```text
Status Code: 200
```

### Phase 2 Status

```text
COMPLETED
```

---

# Phase 3 - Messaging Framework & RabbitMQ

RabbitMQ was integrated using the Python **Pika** library.

## Completed Work

### RabbitMQ Configuration

Configured the development RabbitMQ instance with:

- Host
- Port
- Username
- Virtual Host
- Authentication

Sensitive credentials are not included in this README.

### Queue

Created the RabbitMQ queue:

```text
faculty_burnout_queue
```

### Producer

The application publishes ML prediction results to RabbitMQ as JSON messages.

Example:

```json
{
    "Faculty_ID": "FAC2001",
    "predicted_burnout_risk": "Medium",
    "risk_probabilities": {
        "High": 0.2198,
        "Low": 0.018,
        "Medium": 0.7622
    }
}
```

### Consumer

A separate Python consumer retrieves messages from:

```text
faculty_burnout_queue
```

The consumer:

1. Receives the message.
2. Decodes the JSON message.
3. Reads the prediction.
4. Processes the result.
5. Acknowledges the message.

### RabbitMQ Connection Handling

During development, a RabbitMQ connection reset occurred. It was handled by improving the connection configuration with:

- Heartbeat.
- Connection timeout.
- Retry attempts.
- Fresh connections for publishing.
- Separate consumer connection.

### Phase 3 Flow

```text
Producer
   |
   v
RabbitMQ
   |
   v
faculty_burnout_queue
   |
   v
Consumer
   |
   v
Message Processing
```

### Phase 3 Status

```text
COMPLETED
```

---

# Phase 4 - Caching Framework & Redis

Redis was integrated using the Python `redis` library.

## Completed Work

### Redis Configuration

Configured the development Redis instance with:

- Host
- Port
- Username
- Database
- Authentication

Sensitive credentials are not included in this README.

### Redis Connection

Successfully connected to the Redis instance.

### Cache Operations

The following operations were implemented and verified:

```text
SET     - Store prediction data
GET     - Retrieve prediction data
UPDATE  - Update cached data
DELETE  - Delete cached data
```

### Cache Key Example

```text
faculty_burnout:FAC001
```

### TTL

Prediction results are stored with a cache expiration time.

### Phase 4 Status

```text
COMPLETED
```

---

# Phase 5 - End-to-End Integration

Phase 5 combines the ML model, Flask API, Redis, RabbitMQ, and consumer into one workflow.

## Integrated Components

```text
Random Forest ML
        +
Flask REST API
        +
Redis Cache
        +
RabbitMQ
        +
RabbitMQ Consumer
```

## End-to-End Workflow

### Step 1 - API Request

Client sends faculty information to:

```text
POST /predict
```

### Step 2 - Redis Cache Check

The Flask API checks Redis using:

```text
faculty_burnout:<Faculty_ID>
```

### Step 3 - Cache HIT

If the result exists:

```text
Redis
  |
  v
Cached Result
  |
  v
API Response
```

No new ML prediction is required.

### Step 4 - Cache MISS

If the result does not exist:

```text
Redis
  |
  v
CACHE MISS
  |
  v
Random Forest Model
```

### Step 5 - ML Prediction

The model predicts:

```text
Low
Medium
High
```

and calculates the corresponding risk probabilities.

### Step 6 - Store Result in Redis

The prediction is stored in Redis.

```text
ML Result
   |
   v
Redis Cache
```

### Step 7 - Publish to RabbitMQ

The prediction is converted to JSON and published to:

```text
faculty_burnout_queue
```

### Step 8 - RabbitMQ Consumer

The consumer receives the message.

```text
RabbitMQ
   |
   v
Consumer
```

### Step 9 - Message Processing

The consumer processes:

```text
Faculty_ID
Burnout_Risk
Risk_Probabilities
```

### Step 10 - API Response

The Flask API returns the prediction to the client.

---

# Phase 5 Testing

## Cache MISS Test

Tested faculty:

```text
FAC2001
```

Result:

```text
CACHE MISS
Result stored in Redis.
RabbitMQ message published successfully!
Status Code: 200
```

Prediction:

```text
Medium
```

## Cache HIT Test

The same faculty request was sent again.

Result:

```text
CACHE HIT
Status Code: 200
```

The result was successfully retrieved from Redis instead of running the ML prediction again.

## RabbitMQ Consumer Test

Verified:

```text
RabbitMQ Connection: SUCCESS
Queue: faculty_burnout_queue
Consumer Processing: SUCCESS
Processing Status: Processed Successfully
```

---

# Final Verification

```text
ML Model                 SUCCESS
Redis Connection         SUCCESS
Redis Cache              SUCCESS
RabbitMQ Connection      SUCCESS
RabbitMQ Queue           SUCCESS
Message Publishing       SUCCESS
Message Consumption      SUCCESS
Message Processing       SUCCESS
Flask API                SUCCESS
Cache HIT                SUCCESS
Cache MISS               SUCCESS
End-to-End Integration   SUCCESS
```

---

# Project Structure

```text
Faculty Burnout & Workload Balancer
|
+-- Dataset Generation
|   +-- dataset_generation.ipynb
|
+-- ML Training
|   +-- RandomForest_train.ipynb
|
+-- ML Testing
|   +-- RandomForest_test.ipynb
|
+-- Flask API
|   +-- RandomForest_API.ipynb
|
+-- RabbitMQ Integration
|   +-- RandomForest_RabbitMQ.ipynb
|
+-- RabbitMQ Consumer
|   +-- RabbitMQ_Consumer.ipynb
|
+-- Generated Files
    +-- faculty_workload_dataset.csv
    +-- faculty_burnout_model_pipeline.pkl
```

---

# Project Notebooks

## Dataset Generation

https://colab.research.google.com/drive/1H62ejmaQou7oZp3ZrbZlhbCsAhpa1Iwa?usp=sharing

## Random Forest Training

https://colab.research.google.com/drive/1bHe5q8YthHXo-Gjn1jk_FZtDK2dG5xyC?usp=sharing

## Random Forest Testing

https://colab.research.google.com/drive/1NWg3MIQD-ytfDpCjqpVJI3545LZO-26z?usp=sharing

## Flask API

https://colab.research.google.com/drive/1fxFzmwDIPXEUxugSSH8kgKnvjX_e5WA8?usp=sharing

## RabbitMQ Integration

https://colab.research.google.com/drive/1xVNIU_b8j-IkUmkltDNlkx8vdcAZCswL?usp=sharing

## RabbitMQ Consumer

https://colab.research.google.com/drive/1psbtX9hDVj3ewHBrKjRYMW5Gp-Zx4_qA?usp=sharing

## Google Drive Project Folder

https://drive.google.com/drive/folders/1AyesntriFm2azWtibTNyJOfNPCntUsMY?usp=sharing

---

# Overall Project Status

| Phase | Component | Status |
|---|---|---|
| Phase 1 | Dataset Generation | Completed |
| Phase 1 | Data Preprocessing | Completed |
| Phase 1 | Random Forest Training | Completed |
| Phase 1 | Model Saving | Completed |
| Phase 2 | Model Testing | Completed |
| Phase 2 | Flask REST API | Completed |
| Phase 2 | API Testing | Completed |
| Phase 3 | RabbitMQ Configuration | Completed |
| Phase 3 | Producer | Completed |
| Phase 3 | Consumer | Completed |
| Phase 3 | Message Processing | Completed |
| Phase 4 | Redis Configuration | Completed |
| Phase 4 | SET / GET | Completed |
| Phase 4 | UPDATE / DELETE | Completed |
| Phase 5 | Flask + ML Integration | Completed |
| Phase 5 | Redis + ML Integration | Completed |
| Phase 5 | RabbitMQ + Flask Integration | Completed |
| Phase 5 | Consumer Integration | Completed |
| Phase 5 | Cache HIT / MISS | Completed |
| Phase 5 | End-to-End Testing | Completed |

---

# Current Architecture

```text
                  Faculty Data
                       |
                       v
               Flask REST API
                       |
                       v
                 Redis Cache
                /           \
          CACHE HIT       CACHE MISS
              |                |
              |                v
              |         Random Forest
              |            ML Model
              |                |
              |                v
              |          Store in Redis
              |                |
              |                v
              |            RabbitMQ
              |                |
              |                v
              |            Consumer
              |                |
              |                v
              |       Message Processing
              |                |
              +-------> API Response
```

---

# Conclusion

The **Faculty Burnout & Workload Balancer** has successfully completed **Phases 1, 2, 3, 4, and 5**.

The current implementation provides:

- Machine Learning-based burnout prediction.
- Flask REST API.
- Redis caching with HIT/MISS handling.
- RabbitMQ message publishing.
- RabbitMQ message consumption.
- Message processing.
- Complete end-to-end integration.

## Current Project Stage

```text
END-TO-END INTEGRATION COMPLETED
```
