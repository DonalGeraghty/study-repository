## **REST Operations** {#rest-operations}

REST operations, which stands for **Representational State Transfer**, are a set of standardized **HTTP methods** used to interact with resources on a server. The most common REST operations map to the **CRUD** (Create, Read, Update, Delete) paradigm.

### **Create: POST** {#create:-post}

The `POST` method is used to **create a new resource** on the server. When you send a `POST` request, you include data in the body of the request, and the server typically responds with a `201 Created` status code and the URI of the newly created resource.

### **Read: GET** {#read:-get}

The `GET` method is used to **retrieve or read** a representation of a resource. `GET` requests should not have a body and should not change the state of the server. They are considered "safe" and **idempotent**, meaning you can call them multiple times without causing any side effects.

### **Update: PUT and PATCH** {#update:-put-and-patch}

The `PUT` and `PATCH` methods are used to **update an existing resource**.

* `PUT` is for **complete replacement**. It's used to replace an entire resource with the data provided in the request body. If the resource doesn't exist, a `PUT` request can sometimes create it.  
* `PATCH` is for **partial updates**. It's used to apply a partial modification to a resource. For example, if you only want to update a user's email address, you would use `PATCH` and only send the new email value.

### **Delete: DELETE** {#delete:-delete}

The `DELETE` method is used to **delete a specified resource** from the server. A successful `DELETE` request typically returns a `204 No Content` status code.

## **REST Response Codes** {#rest-response-codes}

### **1xx: Informational** {#1xx:-informational}

These codes indicate that the request was received and is continuing. They are provisional responses.

* **100 Continue**: The server has received the request headers and the client should proceed to send the request body.  
* **101 Switching Protocols**: The client has asked the server to switch protocols and the server has agreed to do so.

### **2xx: Success** {#2xx:-success}

These codes indicate that the request was successfully received, understood, and accepted.

* **200 OK**: The request has succeeded. This is the most common status code.  
* **201 Created**: The request has been fulfilled and a new resource has been created as a result.  
* **202 Accepted**: The request has been accepted for processing, but the processing has not been completed.  
* **204 No Content**: The server successfully processed the request, but is not returning any content.  
* **205 Reset Content**: The server successfully processed the request, but the client should reset the document view.  
* **206 Partial Content**: The server is delivering only part of the resource due to a range header sent by the client.

### **3xx: Redirection** {#3xx:-redirection}

These codes indicate that further action needs to be taken by the client to fulfill the request.

* **301 Moved Permanently**: The requested resource has been assigned a new permanent URI.  
* **302 Found**: The requested resource resides temporarily under a different URI.  
* **303 See Other**: The response to the request can be found under a different URI and should be retrieved using a GET method.  
* **304 Not Modified**: The resource has not been modified since the version specified by the request headers. The client can use a cached copy.  
* **307 Temporary Redirect**: The request should be repeated with another URI; however, future requests should still use the original URI.  
* **308 Permanent Redirect**: The request and all future requests should be repeated using a new URI.

### **4xx: Client Error** {#4xx:-client-error}

These codes are for situations where the client appears to have made an error.

* **400 Bad Request**: The server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid request message framing, or deceptive request routing).  
* **401 Unauthorized**: The request requires user authentication.  
* **403 Forbidden**: The server understood the request, but is refusing to fulfill it.  
* **404 Not Found**: The server cannot find the requested resource.  
* **405 Method Not Allowed**: The method specified in the request is not allowed for the resource identified by the URI.  
* **409 Conflict**: The request could not be completed due to a conflict with the current state of the target resource.  
* **410 Gone**: The requested resource is no longer available at the server and no forwarding address is known.  
* **415 Unsupported Media Type**: The server refuses to accept the request because the payload format is not supported by the resource.

### **5xx: Server Error** {#5xx:-server-error}

These codes indicate that the server failed to fulfill an apparently valid request.

* **500 Internal Server Error**: The server encountered an unexpected condition that prevented it from fulfilling the request. This is a generic "catch-all" status code.  
* **501 Not Implemented**: The server does not support the functionality required to fulfill the request.  
* **502 Bad Gateway**: The server, while acting as a gateway or proxy, received an invalid response from an inbound server it accessed in attempting to fulfill the request.  
* **503 Service Unavailable**: The server is currently unable to handle the request due to a temporary overload or scheduled maintenance.  
* **504 Gateway Timeout**: The server, while acting as a gateway or proxy, did not receive a timely response from the upstream server specified by the URI.