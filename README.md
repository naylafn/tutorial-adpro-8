# Tutorial Pemrograman Lanjut
## Nayla Farah Nida - 2306213426

### Module 8

**1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?**

Unary RPCs are like a one-time request and reply, the client sends a message and the server responds once. 
Server streaming means the client sends one request, but the server replies with multiple messages over time. 
Bidirectional streaming lets both the client and server send multiple messages back and forth at the same time, like a conversation.

Use Unary when we only need a single answer for a single question (fetch an item from database or authenticate user), 
Server Streaming for getting multiple information or updates over time (watching logs), and Bi-Directional to continuously send and receive data (real-time chat).

**2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?**

In gRPC, especially with streaming (server or bidirectional), each message sent over the stream may need repeated authentication and authorization checks to ensure that only permitted parties continue to send or receive data throughout the session. This is different from REST, where each request is independent and typically undergoes a single validation step before the server processes it—since the connection is closed after the response.

Similarly, for encryption, gRPC transmits data as a stream of multiple messages, so each fragment must be securely encrypted to maintain privacy across the entire session. In contrast, REST typically encrypts the entire request/response in a single transaction, which is simpler to manage.

**3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?**

- ```Asynchronous Concurrency Management```: Both client and server can send messages at any time that without proper task coordination can cause race conditions, deadlocks, or dropped messages.

- ```Backpressure and Flow Control```: Uncontrolled message flow can crash  application or cause timeouts in slow clients.

- ```Timeouts and Connection Management```: We need logic to close or timeout inactive sessions, or the server may waste resources.

**4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?**

Advantages:

- Automatically respects gRPC flow control, the stream will pause sending when the client is not ready, avoiding overwhelming clients.

- Allows decoupling of message production from the gRPC handler.

- Enables custom logic to decide when and what to send.

Disadvantages:

- Manual stream lifecycle management, if not managed properly can lead to deadlocks or hanging streams.

- Lack of built-in error propagation.

- If the sender is held elsewhere and not dropped when the client disconnects, the stream may never terminate, consuming memory or CPU.

**5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?**

The structure should follow clean separation of concerns, encapsulation, and convention-based organization. This could be done by making a modular folder structure, separate gRPC layer from business logic, encapsulate stream logic, and test core logic independently. 

**6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?**

- Add input validation to prevent bad data entering the system, and authentication/authorization to prevent unauthorized transactions

- Make unique request IDs to prevent double charging

- Make a robust error handling and add unit tests for logic, mocks for APIs

**7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?**

gRPC uses Protocol Buffers (Protobuf) to define service contracts, which leads to a more consistent and explicit definition of messages and services. This contract-driven approach promotes clearer service boundaries and better documentation, making it easier to maintain large-scale systems with many microservices.

However, the use of gRPC also introduces interoperability challenges, especially when integrating with systems that do not natively support Protobuf or HTTP/2. This means architects must often plan for hybrid interfaces or adapters when building systems that span multiple platforms or when backward compatibility with REST is needed.

**8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?**

Advantages:

- Multiple requests and responses can be in flight simultaneously over a single TCP connection.

- Reduces overhead by compressing HTTP headers, which is useful in high-frequency communication.

- Full-duplex communication (as seen in gRPC bidirectional streaming) without needing workarounds like WebSocket.

Disadvantages:

- Binary protocol is harder to inspect with tools like ```curl``` or browser dev tools. 

- gRPC itself is not fully supported in browsers due to reliance on HTTP/2 trailers and custom framing.

- HTTP/2 server push is not widely used or well-supported in browsers, and gRPC-Web only supports unary and server streaming with workarounds.

**9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?**

REST is pull-based and stateless, which limits real-time capabilities, while gRPC streaming is push-friendly and stateful, making it much better suited for responsive, ongoing interactions between services or users.

**10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?**

Protobuf’s schema-based model in gRPC fosters consistency, speed, and long-term maintainability, making it ideal for complex internal APIs or systems at scale. JSON’s schema-less nature favors ease of use and quick integration, which is better suited for early-stage development, public APIs, and broad compatibility. 
