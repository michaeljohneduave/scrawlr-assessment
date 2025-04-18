## Scrawlr Technical Assessment

### Question 1: Scaling Web Worker Messaging

Message routing between web workers and the main thread can be implemented in several ways, depending on the specific use case and the technologies involved.

#### Central Messaging Router

*   The router listens directly to incoming messages from the web worker.
*   It provides methods for main thread components to register and unregister as listeners (or subscribers).
*   The specific implementation within components can vary, depending on whether vanilla JavaScript (perhaps with a templating library), React, or Vue.js is used.
*   Implementing the router as a singleton ensures a single point of control, preventing potential state conflicts and adhering to the single responsibility principle.

#### Vanilla JS

*   Plain JavaScript components can call the router's `addMessageListener` method, registering interest in specific topics or tasks.
*   When the worker sends a relevant message, the router forwards it to the registered component's designated message handler method, which can then update the UI accordingly.

#### React/VueJS

*   For React or Vue.js components (both class-based and functional), a similar registration process applies. However, a common pattern is to integrate with a state management library.
*   The router updates the central store, and components automatically re-render based on state changes. Alternatively, React Context can serve this purpose without needing an external library.
*   Using a central store simplifies how components receive updates; they subscribe to relevant state slices rather than directly registering listeners with the router. This is analogous to how React Hooks simplified state logic compared to older patterns.
*   A hybrid approach is also possible, combining a central store for global state with direct component listeners for specific, localized events, depending on the requirements.
*   Furthermore, the router's responsibilities could be separated into distinct modules: one for managing the worker lifecycle (creation/termination) and another for handling message routing (publish/subscribe).

![Question 1 diagram](/web-worker-messaging.png)

### Question 2: Handling API Downtime Notifications

In a system with multiple services, likely fronted by an API Gateway for routing and connection management, handling service downtime requires a dedicated approach.

*   Implementing a dedicated status page/API, separate from the core application infrastructure, provides a scalable and robust way to decouple service health monitoring.
*   This status API, potentially accompanied by a user-facing status page displaying service availability and uptime, serves as the endpoint for clients to subscribe to real-time status updates via Server-Sent Events (SSE).
*   Ensuring the high availability of this status API is crucial. This can be achieved through various methods, ranging in complexity (e.g., using Kubernetes and Consul for higher complexity, or Nginx with Keepalived for simpler setups), to guarantee clients can receive status updates even if parts or all of the main platform are unavailable.
*   On the client-side, two primary options for receiving real-time updates from the status API are WebSockets and Server-Sent Events (SSE). SSE is often preferred for its relative simplicity in this unidirectional scenario.
*   Integrating with a client-side state management solution (e.g., a central store), the application can subscribe to these SSE events and update the UI or disable relevant features based on the received status information.
*   Crucially, this architecture must also ensure the high availability of the main API Gateway itself, as it remains a critical component for overall service accessibility.

![Question 2 diagram](/api-downtime-triggers.png)