## Scrawlr Technical Assessment

### Question 1: Scaling web worker messaging
We could implement message routing in a number of ways and it will depend on the use case and maybe framework?
Central messaging router.
    - Router will directly listen to the incoming messages from the web worker
    - This router will then provide methods to register and unregister listeners/consumers from components of the main thread.
    - Component messaging implementation could vary depending if we just use regular javascript and a templating library or React or VueJS
    - This router will be a singleton to avoid bugs and promote single responsibility.

Vanilla JS
    - Components(plain js objects) can call the router's addMessageListener method and register to a specific topic or task
and when the worker sends a message it can call its message method and send the message to the component and adjust the UI accordingly

React/VueJS
    - Same for react components (both class and functional) but the difference would that we can potentially just use a state management library to listen to the messages sent by the router and the components will adjust to the messages accordingly. We can even leverage React Context if we don't want to deal with a state manager 
    - The store simplifies the registration of each component, much like just hooks simplified the data flow of applications using the redux-style implementation.
    - We can also use a hybrid approach of using a single store and also attach listeners to the react components themselves but this will depend on the use case.
    - Furthermore, the router can also be divided into two parts, worker management (add / remove) and message routing (publish / subscribe)
       
![Question 1 diagram](/web-worker-messaging.png)

### Question 2: API downtime triggers
Since we are dealing with multiple services and these services will most likely be behind an api gateway to properly route and manage client connections.
- Adding a status page that is separate from the api-land would be a scalable and robust way to decouple the status checking of each service.
- This status api, which can also have an actual page for users to see service availability and uptime, will serve as the api for the clients to subscribe for server sent events.
- High availability of the status api will depend on complexity of its implementation (high: kubernetes, consul. low: nginx, keepalived) to ensure that the clients have virtually no downtime even if the some services or all of the platform isn't available.
- As for the client, there would be two options, using websockets to the status api or server side events, I would personally use SSE for its simplicity. Using a single store implementation, the client can listen to server sent events coming from the status api and toggle the affected parts of the client depending on the events received.
- This setup also must consider the high availability of the api gateway

![Question 2 diagram](/api-downtime-triggers.png)