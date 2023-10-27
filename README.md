![Skunkwolf Streaming](./images/skunkwolf-event-stream.png)

# Courtyard
Centralized sreaming and communication solution for the Skunkwolf microservices. Courtyard represents the central gathering place in a castle, it is the hub for communication. 

Skunkwolf uses [NATS](https://nats.io/) and it's [JetStream](https://docs.nats.io/nats-concepts/jetstream). NATS is described as "an open source high performance messaging system and connective fabric". It simplifies communication for the collection of microservices that Skunkwolf uses. 

This project is configured to use a 3 node cluster with JetStream enabled in each node. Communication between the nodes will also leverage TLS via self signed certificates. The process for creating these certificates can be found within the [skunkwolf/armory](https://github.com/skunkwolf/armory) github repository. 

## Table of Contents
- [Getting Started](#getting-started)
- [TLS and NATS](#tls-and-nats)
- [NATS and Kubernetes](#nats-and-kubernetes)
- [Contributing](#contributing)
- [License](#license)

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites
- Kubernetes and Helm installed on your local machine.
- Basic knowledge of 
    - YAML
    - Helm
    - Kubernetes
    - Kubernetes Secrets
    - TLS configuration with self signed certifications
    - Command-line operations

### Clone the Repository
```bash
$ git clone https://github.com/skunkwolf/courtyard.git
$ cd <repository-directory>
```

## TLS and NATS

When using TLS with NATS the [offical documentation](https://artifacthub.io/packages/helm/nats/nats#tls-considerations) states this:

 The TLS Certificate used for Client Connections should have a SAN covering DNS Name that clients access the service at.

 The TLS Certificate used for Cluster Route Connections should have a SAN covering the DNS Name that routes access each other on the headlessService at. This is *.<headless-service-name> by default.

 For configuring the Subject Alternative Name (SAN) in your NATS cluster, you should use the DNS names or IP addresses that clients and other NATS servers will use to connect to the NATS servers in your cluster. Here's a breakdown of what you might consider for the SAN configuration in your NATS cluster setup:

 1. **Client Connections**: 
  - The SAN should include the DNS names or IP addresses that clients will use to connect to your NATS servers.
  - If you have a load balancer or a DNS name that resolves to the IP addresses of your NATS servers, include this DNS name in the SAN.

 2. **Cluster Route Connections**:
  - The SAN should include the DNS names or IP addresses that other NATS servers will use to connect to the NATS servers in your cluster for inter-server communication.
  - Usually, this would be the internal DNS names or IP addresses of the NATS servers within your Kubernetes cluster.

 3. **Wildcard Entries**:
  - If your NATS cluster will have dynamically changing DNS names or IP addresses (for example, due to autoscaling), you might consider using wildcard entries in the SAN. 
  - However, be aware that wildcard entries can potentially be less secure, as they can allow connections to unintended services if not managed carefully.

 Here's a hypothetical example:

 Suppose you have a NATS cluster with three nodes, and they are accessible internally within your Kubernetes cluster at `nats-0.nats.default.svc.cluster.local`, `nats-1.nats.default.svc.cluster.local`, and `nats-2.nats.default.svc.cluster.local`. Additionally, suppose you have a load balancer set up at `nats.example.com` for client connections. Your SAN configuration might look like this:

 ```plaintext
 DNS:nats-0.nats.default.svc.cluster.local, DNS:nats-1.nats.default.svc.cluster.local, DNS:nats-2.nats.default.svc.cluster.local, DNS:nats.example.com
 ```

 This SAN configuration includes both the internal DNS names for inter-server communication and the external DNS name for client connections.

 When generating certificates for your NATS servers, you would use this SAN configuration to ensure that the certificates are valid for both client connections and inter-server communication within your NATS cluster.

 Now, when you run your script to generate service certificates, you would specify this SAN configuration using the `-s` option, like so:

 ```bash
 generate-nats-certs.sh -c "NATS Cluster" -o nats-cluster -u "NATS" -s "DNS:nats-0.nats.default.svc.cluster.local, DNS:nats-1.nats.default.svc.cluster.local, DNS:nats-2.nats.default.svc.cluster.local, DNS:nats.example.com"
 ```

 This will generate a service certificate with a SAN that covers both client connections and inter-server communication within your NATS cluster.

The repository [skunkwolf/armory](https://github.com/skunkwolf/armory) allows the creation of certificates with a Subject Alternative Name (SAN) using the `-s` flag

## NATS and Kubernetes 

The offical documentation on running NATS with Kubernetes can be found [here](https://docs.nats.io/running-a-nats-service/nats-kubernetes). This section provides a step by step guide on how to configure this in a 3 node cluster with TLS enabled. 

### Step 1. Register the NATS helm chart
```bash 
helm repo add nats https://nats-io.github.io/k8s/helm/charts/
helm repo update
```

### Step 2. Update the required configuration

The [nats-config.yaml](./nats-config.yaml) file contains the default configuration for **Skunkwolf**. 

In this file:

- The first part defines a headless service for NATS.
- The second part (separated by ---) contains the Helm chart configuration for NATS.
- The third part (also separated by ---) defines a Kubernetes secret to hold your TLS certificates and keys.

Within this file the following are set: 
- Set cluster.enabled to true to enable clustering.
- Set cluster.replicas to 3 for a 3-node cluster.
- Enable TLS for the cluster with cluster.tls.enabled set to true.
- Specify the Kubernetes secret containing your TLS certificates with cluster.tls.secret.name.
- Enable JetStream with jetstream.enabled set to true, and configure memory and file storage for JetStream.


Changes can be made to this configuration as needed. 

### Step 3. Deploy NATS 

In order to deploy NATS with the update configuration use this command: 

```bash
helm install skunkwolf-nats nats/nats -f nats-config.yaml
```

### Step 4. Verify that the NATS Pods are running

```bash
kubectl get pods
```

[Back to Top](#table-of-contents)

## Contributing

If you wish to contribute to this project, please feel free to fork the repo, create a new branch, commit your changes, and open a pull request.

[Back to Top](#table-of-contents)

## License

This project is licensed under the MIT License - see the LICENSE.md file for details.

[Back to Top](#table-of-contents)