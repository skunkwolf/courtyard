![Skunkwolf Streaming](./images/skunkwolf-event-stream.png)

# Courtyard
Centralized sreaming and communication solution for the Skunkwolf microservices. Courtyard represents the central gathering place in a castle, it is the hub for communication. 

Skunkwolf uses [NATS](https://nats.io/) and it's [JetStream](https://docs.nats.io/nats-concepts/jetstream). NATS is described as "an open source high performance messaging system and connective fabric". It simplifies communication for the collection of microservices that Skunkwolf uses. 

This project is configured to use a 3 node cluster with JetStream enabled in each node. Communication between the nodes will also leverage TLS via self signed certificates. The process for creating these certificates can be found within the [skunkwolf/armory](https://github.com/skunkwolf/armory) github repository. 

## Table of Contents
- [Getting Started](#getting-started)
- [Contributing](#contributing)
- [License](#license)

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites
- Kubernetes installed on your local machine.
- Basic knowledge of 
    - YAML
    - HELM
    - Kubernetes
    - Kubernetes Secrets
    - TLS configuration with self signed certifications
    - Command-line operations

### Clone the Repository
```bash
$ git clone https://github.com/skunkwolf/courtyard.git
$ cd <repository-directory>
```











## Contributing

If you wish to contribute to this project, please feel free to fork the repo, create a new branch, commit your changes, and open a pull request.

[Back to Top](#table-of-contents)

## License

This project is licensed under the MIT License - see the LICENSE.md file for details.

[Back to Top](#table-of-contents)