# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. RwLock here is equivalent to a Read-Write lock. This lock allows multiple readers to access data simultaneously, but limits the write ability to one thread at a time. On the other side, a Mutex only allows one thread at a time to have read or write access. In our specific case, RwLock is preffered over Mutex because users will frequently check/read notifications, but receiving a new notification (write) isn't as frequent, since it comes from the publisher. A Mutex would block multiple users trying to see notifications simultaneously, which is bad performance. RwLock allows multiple users to read at the same time.
2. This matter relates to Rust's memory and thread safety during compile time. In Java, mutable static variables can lead to race conditions and undefined behaviours if multiple threads have access to it concurrently. Rust's compiler rejects this scenario since it is not thread safe. Additionally, lazy_static is used to allow static variables to be initialized when accessed during runtime (not at compile time), but in a thread safe manner.
#### Reflection Subscriber-2
1. I was naturally curious about the default project files such as the lib.rs file along with main.rs in src. At first glance, I couldn't understand it, but it looks like the project settings/configurations. After exploring more, turns out that the lib.rs file acts as a root for the library package, handles rocket configurations, app managements, and CORS settings. On the other hand, main.rs is responsible for executing the app. 
2. The observer pattern really helps in managing the subscriptions. The publisher app doesn't even need to specifically handle each subscriber, it already has a uniformed way to send notifications. To add a new subscriber, the system simply adds a new URL to the DashMap in the publisher app. The publisher's code doesn't change at all. The unsubscribing process is more or less the same way, the receiver instance's url is just removed from the publisher's DashMap so they won't receive anymore notifications. However, spawning more than one instance of the Main app may cause some problems. It will seems like two different publishers having no knowledge of each other existing. If a subscriber subscribes to Main A, then Main B won't know that the subscriber already subscribed to Main A. This causes a difference in databases. If we intend to send a notification to our subscribers through Main B, but the subscribers are subscribed to Main A, then they won't receive any notifications. A solution to this is maybe to use a centralized database like PostgreSQL where each main instance can query to instead of a local DashMap. 
3. While I have not yet tried creating my own automated tests or enhancing documentation within my Postman collection, I am highly eager to explore more of these features. I am planning to start writing Postman tests to verify HTTP status codes (201, 200, etc) and validating JSON responses. This way, I can stop manually validating the tests and automatically run the test with validations. This can prove useful in a group project, since everyone can use these tests to verify their codes automatically to minimize errors and have a clearer API contract to work with. 