## **Endpoint: Contract Analysis Summary**

---

#### **Endpoint**  

`GET /contract/{contract_address}`

#### **Description**  

This endpoint retrieves a SmartMuv analysis summary for a specified smart contract. If the analysis is already present in the database, it is fetched and returned. Otherwise, the input is validated, and a new analysis task is provided. 

Status of the contract then can be checked by querying again.

(Optional) The task status can also be tracked using the returned task ID using get task status.



---



#### **Request**

##### **Method**  

`GET`

##### **Path Parameters**

| Parameter          | Type     | Required | Description                                                  |
| ------------------ | -------- | -------- | ------------------------------------------------------------ |
| `contract_address` | `string` | Yes      | The smart contract's address to analyze. Must be a valid Ethereum address. |

---



#### **Response**

##### 1. **Successful Response**  

If the contract analysis is already present in the database or the analysis is successfully completed, the response contains a summary of the contract analysis.

- **Status Code**: `200 OK`  
- **Format**: `application/json`  
- **Sample**:

```json
{
  "success": true,
  "status": "ok",
  "message": "ok",
  "data": {
    "total_mapping": 0,
    "total_variables": 0,
    "total_key_sources": 0,
    "complexity_score": 6.1875
  }
}
```

##### **Response Fields**

| Field               | Type      | Description                                                  |
| ------------------- | --------- | ------------------------------------------------------------ |
| `total_mapping`     | `integer` | Total number of mappings in the contract.                    |
| `total_variables`   | `integer` | Total number of variables in the contract.                   |
| `total_key_sources` | `integer` | Total number of mapping variable's key sources in the contract. |
| `complexity_score`  | `float`   | Calculated complexity score of the contract.                 |




2. **Task Created for Analysis**

   - **Status Code**: `202 Accepted`

   - **Sample**:

     ```json
     {
       "success": true,
       "status": "accepted",
       "message": "Task is being processed. Please check later for results.",
       "task_id": "abc12345"
     }
     ```

   

3. **Task is in Queue**

- **Status Code**: `202 OK`
- **Format**: `application/json`
- **Sample**:

```json
	{
        "success": true,
        "status": "queued",
        "message": "Task is already queued and being processed.",
	}
```

---



#### **Error Responses**

1. **Invalid Contract Address**

   - **Status Code**: `400 Bad Request`

   - **Sample**:

     ```json
     {
       "success": false,
       "status": "error",
       "message": "Invalid contract address format. A contract address must be 42 characters long, start with '0x', and contain only hexadecimal characters."
     }
     ```


2. **Internal Server Error**

   - **Status Code**: `500 Internal Server Error`

   - **Sample**:

     ```json
     {
       "success": false,
       "status": "error",
       "message": "An error occurred during contract analysis. Please try again later."
     }
     ```



#### **Notes**

- The complexity score is computed based on predefined metrics of the contract's structure and behavior.
- Results may take additional time to process if not already in the database.




---

### **Endpoint: Get Task Status**

#### **Endpoint**

```
GET /task_status/{task-id}
```

#### **Description**

This endpoint retrieves the status of a task created for contract analysis. If the task is completed, it provides the analysis results.

---



#### **Request**

##### **Method**

```
GET
```

##### **Path Parameters**

| Parameter | Type     | Required | Description                        |
| --------- | -------- | -------- | ---------------------------------- |
| `task_id` | `string` | Yes      | The unique identifier of the task. |

---



#### **Response**

##### **Successful Response**

1. **Task in Progress**

   - **Status Code**: `200 OK`

   - **Format**: `application/json`

   - **Sample**:

     ```json
     {
       "success": true,
       "status": "Processing",
       "message": "Task is still being processed.",
       "task_id": "abc12345"
     }
     ```

2. **Task Completed Successfully**

   - **Status Code**: `200 OK`

   - **Format**: `application/json`

   - **Sample**:

     ```json
     {
       "success": true,
       "status": "Completed",
       "message": "Task completed successfully.",
       "data": {
         "total_mapping": 0,
         "total_variables": 0,
         "total_key_sources": 0,
         "complexity_score": 6.1875
       }
     }
     ```



---



##### **Error Responses**

1. **Task Failed**

   - **Status Code**: `404 OK`

   - **Format**: `application/json`

   - **Sample**:

     ```json
     {
       "success": false,
       "status": "Results Not Found",
       "message": "No results found for the provided contract address."
     }
     ```

2. **Internal Server Error**

   - **Status Code**: `500 Internal Server Error`

   - **Sample**:

     ```json
     {
       "success": false,
       "status": "Error",
       "message": "An unexpected error occurred. Please try again later."
     }
     ```

     


#### **Notes**

- This endpoint is designed to handle asynchronous tasks in a scalable manner.
- The `task_id` is required to identify and track the status of specific tasks.
- If `"status": "Completed"`, the returned data structure is the same as the one for `/contract/{contract_address}`.

