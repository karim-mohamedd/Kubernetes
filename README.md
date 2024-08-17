
# Kubernetes Practice Repository

Welcome to the Kubernetes Practice Repository! This repository is designed to help you gain practical experience with Kubernetes, the leading container orchestration platform. Whether you're new to Kubernetes or looking to enhance your skills, this guide provides a structured approach to learning and practicing Kubernetes concepts.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Getting Started](#getting-started)
3. [Practice Exercises](#practice-exercises)
4. [Additional Resources](#additional-resources)
5. [Contributing](#contributing)
6. [License](#license)

## Prerequisites

Before you start, make sure you have the following tools and knowledge:

- **Kubernetes CLI (kubectl)**: Install the Kubernetes CLI by following the [official installation guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
- **Kubernetes Cluster**: Set up a Kubernetes cluster. You can use Minikube for local development or a managed Kubernetes service like Google Kubernetes Engine (GKE), Amazon EKS, or Azure Kubernetes Service (AKS).
- **Helm (optional)**: Helm is a package manager for Kubernetes. Install it from [here](https://helm.sh/docs/intro/install/).
- **Git**: Ensure Git is installed to clone the repository. Download it from [here](https://git-scm.com/downloads).

## Getting Started

1. **Clone the Repository**

   ```bash
   git clone https://github.com/karim-mohamedd/kubernetes-practice.git
   cd kubernetes-practice
   ```

2. **Explore the Repository Structure**

   The repository contains the following key folders:

   - `manifests/`: Kubernetes YAML manifest files for various resources like Pods, Deployments, Services, etc.
   - `charts/`: Helm charts for deploying applications on Kubernetes.
   - `scripts/`: Utility scripts for setup and management.

3. **Set Up Your Kubernetes Environment**

   - **Using Minikube**: Follow the [Minikube installation guide](https://minikube.sigs.k8s.io/docs/start/) to set up a local cluster.
   - **Using a Managed Service**: Follow the respective documentation for GKE, EKS, or AKS to create a cluster.

4. **Deploy Resources**

   Navigate to the `manifests/` folder or use Helm charts from `charts/`. Here are some basic commands to get you started:

   - **To apply a Kubernetes manifest file**:

     ```bash
     kubectl apply -f path/to/manifest.yaml
     ```

   - **To check the status of your deployments**:

     ```bash
     kubectl get pods
     kubectl get services
     ```

   - **To use Helm to install a chart**:

     ```bash
     helm install my-release charts/my-chart
     ```

5. **Verify the Setup**

   - **Check Pods**: Ensure that your pods are running and healthy.

     ```bash
     kubectl get pods
     ```

   - **Access Services**: If your services are exposed, you can access them using the provided service URLs or by port forwarding.

     ```bash
     kubectl port-forward service/my-service 8080:80
     ```

## Practice Exercises

Here are some exercises to help you practice Kubernetes concepts:

1. **Pod and Deployment Basics**: Create a simple Pod and Deployment using a YAML file. Scale the deployment and update the image.

2. **Service Discovery**: Set up a Service to expose your deployment. Explore different types of services like ClusterIP, NodePort, and LoadBalancer.

3. **ConfigMaps and Secrets**: Use ConfigMaps and Secrets to manage configuration and sensitive information for your applications.

4. **Helm Charts**: Create and deploy your own Helm chart. Package the chart and deploy it to your Kubernetes cluster.

5. **Networking**: Set up network policies and experiment with Ingress controllers for managing external access to your services.

6. **Resource Management**: Define resource limits and requests for your containers. Monitor resource usage using Kubernetes metrics.

7. **Persistent Storage**: Set up a PersistentVolume and PersistentVolumeClaim to manage storage for a stateful application.

8. **Rolling Updates and Rollbacks**: Perform rolling updates on a deployment and learn how to roll back to a previous version if needed.

## Additional Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Helm Documentation](https://helm.sh/docs/)
- [Kubernetes Tutorials](https://kubernetes.io/docs/tutorials/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)

## Contributing

We welcome contributions to improve the repository! If you have suggestions for additional exercises or improvements, please open an issue or submit a pull request.

1. Fork the repository
2. Create a new branch (`git checkout -b feature-branch`)
3. Make your changes
4. Commit your changes (`git commit -am 'Add new exercise'`)
5. Push to the branch (`git push origin feature-branch`)
6. Create a new Pull Request



---

Happy Kubernetes-ing! 🌟 If you encounter any issues or have questions, please reach out via GitHub Issues or contact karimelwaraky50@gmail.com.


