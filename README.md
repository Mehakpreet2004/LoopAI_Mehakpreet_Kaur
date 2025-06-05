# Data Ingestion API System

A simple Node.js + Express API for asynchronous data ingestion, batch processing, and status tracking with priority and rate limiting.

---

## Features

- **POST `/ingest`**: Submit a list of IDs and a priority (HIGH, MEDIUM, LOW). IDs are processed in batches of 3, respecting priority and FIFO order.
- **GET `/status/:ingestion_id`**: Check the status of your ingestion request and its batches.
- **Batch Processing**: Only 3 IDs are processed per batch, and only 1 batch is processed every 5 seconds.
- **Priority Handling**: Higher priority batches are processed before lower ones, and batches with the same priority are processed in order of arrival.
- **Simulated External API**: Each ID is "processed" with a 1-second delay to simulate external API work.

---

## How It Works

1. **Ingestion**:  
   - Send a POST request to `/ingest` with a JSON body:
     ```json
     {
       "ids": [1, 2, 3, 4, 5],
       "priority": "HIGH"
     }
     ```
   - You receive a unique `ingestion_id` immediately.

2. **Batching & Processing**:  
   - IDs are split into batches of 3.
   - Batches are queued and processed asynchronously, one batch every 5 seconds, with each ID in a batch taking 1 second to process (simulated).
   - Higher priority batches are always processed first.

3. **Status Tracking**:  
   - Check status with `GET /status/<ingestion_id>`.
   - Response includes overall status and per-batch status (`yet_to_start`, `triggered`, `completed`).

---

## Running Locally

1. **Clone the repository**
   ```sh
   git clone https://github.com/Mehakpreet2004/LoopAI_Mehakpreet_Kaur.git
   cd server
   ```

2. **Install dependencies**
   ```sh
   npm install
   ```

3. **Start the server**
   ```sh
   node index.js
   ```
   The server runs on `http://localhost:5001` by default.

---

## API Endpoints

### 1. Ingestion

**POST** `/ingest`

**Body:**
```json
{
  "ids": [1, 2, 3, 4, 5],
  "priority": "HIGH"
}
```

**Response:**
```json
{
  "ingestion_id": "abc123"
}
```

---

### 2. Status

**GET** `/status/:ingestion_id`

**Response:**
```json
{
  "ingestion_id": "abc123",
  "status": "triggered",
  "batches": [
    { "batch_id": "uuid1", "ids": [1, 2, 3], "status": "completed" },
    { "batch_id": "uuid2", "ids": [4, 5], "status": "triggered" }
  ]
}
```

---

## Design Choices

- **In-memory storage** for simplicity (no DB required).
- **Async batch processor** runs in the background, respects rate limits and priorities.
- **UUIDs** for unique ingestion and batch IDs.
- **Simple error handling** for invalid payloads and missing ingestions.

---

## Testing

- Use [Thunder Client](https://www.thunderclient.com/) or [Postman](https://www.postman.com/) to test the endpoints.
- Example Thunder Client requests:
  - POST `http://localhost:5001/ingest`
  - GET `http://localhost:5001/status/<ingestion_id>`

