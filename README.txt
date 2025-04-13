# AWS 3-Tier Application Setup

This repository provides the necessary configuration and code to deploy a scalable 3-tier web application architecture on AWS. The architecture consists of three main layers: Web, Application, and Database, which are supported by a range of AWS services, ensuring high availability, load balancing, and fault tolerance.

## Architecture Overview

In this architecture, we have three main layers:

- **Web Tier**: Handles client requests and serves the front-end website.
- **Application Tier**: Processes API requests and handles the business logic.
- **Database Tier**: Manages data storage and retrieval.

---

## Key Components

### 1. External Load Balancer (Public-Facing Application Load Balancer)

- **Role**: Acts as the entry point for all client traffic.
- **Functionality**:
  - Distributes incoming client requests to the web tier EC2 instances.
  - Ensures even distribution of traffic for better performance and reliability.
  - Performs health checks to ensure only healthy instances receive traffic.

### 2. Web Tier

- **Role**: Serves the front-end of the application and redirects API calls.
- **Components**:
  - **Nginx Web Servers**: Running on EC2 instances.
  - **React.js Website**: The front-end application served by Nginx.
- **Functionality**:
  - **Serving the Website**: Nginx serves the static files for the React.js application to the clients.
  - **Redirecting API Calls**: Nginx is configured to route API requests to the internal-facing load balancer of the application tier.

### 3. Internal Load Balancer (Application Tier Load Balancer)

- **Role**: Manages traffic between the web tier and the application tier.
- **Functionality**:
  - Receives API requests from the web tier.
  - Distributes these requests to the appropriate EC2 instances in the application tier.
  - Ensures high availability and load balancing within the application tier.

### 4. Application Tier

- **Role**: Handles the application logic and processes API requests.
- **Components**:
  - **Node.js Application**: Running on EC2 instances.
- **Functionality**:
  - **Processing Requests**: The Node.js application receives API requests, performs necessary computations or data manipulations.
  - **Database Interaction**: Interacts with the Aurora MySQL database to fetch or update data.
  - **Returning Responses**: Sends the processed data back to the web tier via the internal load balancer.

### 5. Database Tier (Aurora MySQL Multi-AZ Database)

- **Role**: Provides reliable and scalable data storage.
- **Functionality**:
  - **Data Storage**: Stores all the application data in a structured format.
  - **Multi-AZ Setup**: Ensures high availability and fault tolerance by replicating data across multiple availability zones.
  - **Data Retrieval and Manipulation**: Handles queries and transactions from the application tier to manage the data.

---

## Additional Components

### 1. Load Balancing

- **Purpose**: Distributes incoming traffic evenly across multiple instances to prevent any single instance from becoming a bottleneck.
- **Implementation**:
  - **Web Tier**: The external load balancer distributes traffic to web servers.
  - **Application Tier**: The internal load balancer distributes API requests to application servers.

### 2. Health Checks

- **Purpose**: Continuously monitors the health of instances to ensure only healthy instances receive traffic.
- **Implementation**:
  - **Web Tier**: Health checks by the external load balancer to ensure web servers are responsive.
  - **Application Tier**: Health checks by the internal load balancer to ensure application servers are operational.

### 3. Auto Scaling Groups

- **Purpose**: Automatically adjusts the number of running instances based on traffic load to maintain performance and cost efficiency.
- **Implementation**:
  - **Web Tier**: Auto-scaling based on metrics like CPU usage or request count to add or remove web server instances.
  - **Application Tier**: Auto-scaling based on similar metrics to adjust the number of application server instances.

---

## Setup Instructions

Follow these steps to deploy the AWS 3-tier application:

### 1. Clone the Repository

```bash
git clone https://github.com/your-repo/aws-3-tier-app.git
cd aws-3-tier-app
