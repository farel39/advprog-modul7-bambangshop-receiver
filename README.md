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
1. In this case, we use `RwLock<Vec<Notification>>` to allow concurrent reads while ensuring that writes are synchronized. `RwLock` provides two types of locks: a read lock (`read()`) that allows multiple threads to read simultaneously and a write lock (`write()`) that ensures exclusive access when modifying the data. This is useful because the `list_all_as_string` function only reads the notifications and should not block other read operations, whereas `add` modifies the vector and requires exclusive access. If we had used `Mutex<Vec<Notification>>`, every access—whether reading or writing—would require acquiring a lock, preventing multiple reads from happening at the same time, thus reducing performance in a read-heavy scenario. Using `RwLock` ensures better efficiency when multiple reads occur frequently compared to writes.

2. Rust does not allow direct mutation of static variables like in Java due to its strict ownership and borrowing rules, which are designed to ensure memory safety and prevent data races. In Java, static variables are typically stored in a shared memory space, and multiple threads can mutate them freely, which can lead to race conditions if not properly synchronized. Rust, on the other hand, enforces thread safety at compile time. A `static` variable in Rust must have a `'static` lifetime and is immutable unless wrapped in a type like `RwLock` or `DashMap`, which provides controlled, synchronized access. This design prevents data races and ensures that all mutations occur safely, enforcing Rust’s guarantees of memory safety and concurrency correctness.

#### Reflection Subscriber-2
1. Yes, I have explored things outside of the steps in the tutorial, including `src/lib.rs`, to gain a deeper understanding of how the project is structured. By examining `lib.rs`, I learned how the application initializes its core components, such as setting up routes, managing dependencies, and handling application configuration. Additionally, I explored how different modules interact, including how `lazy_static!` is used to create static instances like `REQWEST_CLIENT` and `APP_CONFIG`, ensuring thread-safe global access. Understanding these additional parts of the code helped me see how `Figment` is used for configuration management, how `dotenvy` is utilized for environment variables, and how `rocket::serde` facilitates JSON serialization and deserialization. This exploration gave me a better grasp of how Rust enforces type safety and modularity in a web application.

2. The Observer pattern makes it easy to plug in more subscribers because it decouples the notification sender (subject) from the receivers (observers). In the notification system, each `Receiver` instance acts as a subscriber that listens for updates from the main application. When a new receiver instance is spawned, it can subscribe dynamically without modifying the existing code, ensuring scalability. This pattern allows us to add or remove subscribers without affecting the publisher's logic, making the system extensible.

    However, if we spawn multiple instances of the Main app, it introduces complexity in maintaining consistency across instances. Since each instance acts as an independent publisher, synchronizing state between them becomes a challenge. If notifications are stored in memory (e.g., using `RwLock<Vec<Notification>>`), each instance will maintain its own local state rather than sharing a global one. To solve this, we would need a shared message broker or database, such as Redis or Kafka, to ensure that all instances receive and distribute notifications consistently. Otherwise, different instances might hold different sets of subscribers, leading to missed or duplicated notifications.

3. I modified the Postman collection to support multiple product types in the Create New Product request. This allowed me to test the notification system across three different receiver instances running on ports 8001, 8002, and 8003. By assigning different product types to each instance, I verified that notifications were correctly filtered and delivered only to the relevant subscribers. This ensured that the Observer pattern was functioning as expected by dispatching notifications accurately based on product type. Enhancing the Postman collection in this way provided a more realistic testing scenario, demonstrating how the system would handle multiple subscribers in a real-world environment.

