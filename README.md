# rxssm

A Rust library for **streaming state-space models (SSMs)** with optional integration for **RxRust** to enable reactive pipelines. **rxssm** allows you to build flexible, real-time data streams, applying stateful and stateless models for continuous updates with a clean, composable API.

## Overview

**rxssm** provides a flexible framework for streaming state-space models in Rust. It offers the following features:
- **Core Library**: Core support for state-space models (e.g., Kalman filters), stateless transformations, and serialization.
- **RxRust Integration**: An optional feature to seamlessly combine RxRust’s reactive pipeline capabilities with state-space models, allowing for easy stream composition and real-time data processing.
  
The library is designed to handle real-time, continuous data streams for applications such as signal processing, financial data analysis, control systems, and more.

### Key Features
- **Streaming State-Space Models (SSMs)**: Continuously update model states as new data arrives.
- **Stateless Transformations**: Easily apply transformations (e.g., normalization) to data streams.
- **Serialization**: Save and restore intermediate states for efficient checkpointing.
- **Reactive Integration**: Enable reactive programming with **RxRust** for flexible data pipelines and observable sequences (available under the `rxrust_integration` feature).

## Installation

To include **rxssm** in your project, add it to your `Cargo.toml`:

### Core Usage (Without RxRust)
```toml
[dependencies]
rxssm = "0.1"
serde = { version = "1.0", features = ["derive"] }
```

### With RxRust Integration
If you want to use **RxRust** for reactive streams, enable the `rxrust_integration` feature:

```toml
[dependencies]
rxssm = { version = "0.1", features = ["rxrust_integration"] }
serde = { version = "1.0", features = ["derive"] }
```

## Example Usage

### Core Library (Without RxRust)

In this example, we create a state-space model and apply a stateless transformation to incoming data:

```rust
use rxssm::models::{KalmanFilter, StatelessTransform};
use serde::{Serialize, Deserialize};

fn main() {
    // Initialize a Kalman Filter (or any state-space model)
    let mut kalman = KalmanFilter::new(0.0);

    // Create a stateless transformation (e.g., scaling the input)
    let scale = StatelessTransform::new(|x| x * 2.0);

    // Process a stream of data
    for data_point in vec![1.0, 2.0, 3.0] {
        let scaled = scale.apply(data_point);
        let output = kalman.update(scaled);
        println!("Updated output: {}", output);
    }

    // Serialize the Kalman filter's state
    let serialized = serde_json::to_string(&kalman).unwrap();
    println!("Serialized state: {}", serialized);
}
```

### RxRust Integration (Reactive Pipeline)

If you enable the `rxrust_integration` feature, you can use **RxRust** to build reactive pipelines that stream data through the state-space models.

```rust
#[cfg(feature = "rxrust_integration")]
use rxrust::prelude::*;
use rxssm::models::KalmanFilter;

#[cfg(feature = "rxrust_integration")]
fn main() {
    let mut kalman = KalmanFilter::new(0.0);

    // Create an observable data stream
    let data_stream = observable::from_iter(vec![1.0, 2.0, 3.0])
        .map(move |data| kalman.update(data))
        .subscribe(|output| println!("Reactive output: {}", output));
}
```

## Features

### Core Features
- **Stateful Models**: Support for state-space models like Kalman filters.
- **Stateless Transformations**: Apply transformations to data streams (e.g., scaling, normalization).
- **Serialization**: Easily save and load model states with `serde`.

### RxRust Integration (Optional)
- Use **RxRust** to build reactive pipelines.
- Process data streams using RxRust observables, allowing for functional composition of data transformations and model updates.

## Serialization and State Management

Serialization of model states is powered by `serde`, allowing for efficient checkpointing and recovery of model states. This is crucial for long-running applications or systems that need fault tolerance.

### Example of Serialization
```rust
use rxssm::models::KalmanFilter;
use serde::{Serialize, Deserialize};

#[derive(Serialize, Deserialize)]
struct MyModel {
    kalman: KalmanFilter,
}

fn main() {
    let model = MyModel {
        kalman: KalmanFilter::new(0.0),
    };

    // Serialize the model state
    let serialized = serde_json::to_string(&model).unwrap();
    println!("Serialized: {}", serialized);

    // Deserialize the model state
    let deserialized: MyModel = serde_json::from_str(&serialized).unwrap();
    println!("Deserialized: {:?}", deserialized.kalman);
}
```

## Contribution

Contributions are welcome! If you'd like to contribute:
- Fork the repo
- Create a new branch for your feature or bugfix
- Submit a pull request with a clear description of the changes

## License

This project is licensed under the MIT License.
