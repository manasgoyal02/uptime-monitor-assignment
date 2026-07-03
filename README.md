# uptime-monitor-assignment
# 🛡️ Uptime Pulse

<p align="center">
  <strong>A lightweight, real-time uptime monitoring application built with React, Node.js, Express, MongoDB, and Mongoose.</strong>
</p>

<p align="center">
  Monitor websites every minute, track availability, record response times, and visualize endpoint health through a modern dark-themed dashboard.
</p>

---

## ✨ Features

* 🌐 Monitor any HTTP/HTTPS endpoint
* ⏱️ Automatic health checks every minute
* 📊 Live response time and HTTP status tracking
* 💾 Stores the latest **100 health checks** per endpoint
* 🚫 Duplicate URL prevention
* 🎨 Premium dark-themed responsive dashboard
* 🐳 One-command Docker deployment
* ☁️ Production deployment architecture included

---

# 🚀 Quick Start

Clone the repository and launch the complete stack with Docker.

```bash
docker compose up --build
```

That's it.

After the containers start:

| Service            | URL                  |
| ------------------ | -------------------- |
| Frontend Dashboard | http://localhost     |
| Backend API        | http://localhost/api |

---

# 🧪 Local Verification

### ✅ 1. Add a Healthy Endpoint

```
https://example.com
```

**Expected**

* 🟢 Status: **UP**
* HTTP Status: **200**
* Response Time displayed in milliseconds

---

### ❌ 2. Add an Invalid Domain

```
http://invalid-url-test.local
```

**Expected**

* 🔴 Status: **DOWN**
* HTTP Status: `-`
* DNS resolution failure

---

### ❌ 3. Test Server Errors

```
https://httpbin.org/status/500
```

**Expected**

* 🔴 Status: **DOWN**
* HTTP Status: **500**

---

### 🚫 4. Verify Duplicate Prevention

Try adding

```
https://example.com
```

again.

**Expected**

A notification appears:

> This URL is already being monitored.

---

# 📂 Project Structure

```
.
├── backend/
│   ├── API
│   ├── Scheduler
│   ├── Health Check Logic
│   └── MongoDB Models
│
├── frontend/
│   ├── React + Vite
│   ├── Dashboard UI
│   └── Components
│
├── docker-compose.yml
├── README.md
└── AI_LOG.md
```

---

# 🏗️ Tech Stack

| Layer            | Technology              |
| ---------------- | ----------------------- |
| Frontend         | React + Vite            |
| Backend          | Node.js + Express       |
| Database         | MongoDB + Mongoose      |
| Scheduler        | Node Cron               |
| HTTP Client      | Axios                   |
| Reverse Proxy    | Nginx                   |
| Containerization | Docker & Docker Compose |

---

# ⚙️ Environment Variables

Create a `.env` file using `.env.example`.

| Variable          | Default                               | Description               |
| ----------------- | ------------------------------------- | ------------------------- |
| `DATABASE_URL`    | `mongodb://localhost:27017/uptime_db` | MongoDB connection string |
| `PORT`            | `5000`                                | Backend server port       |
| `CRON_SCHEDULE`   | `* * * * *`                           | Health check interval     |
| `REQUEST_TIMEOUT` | `10000`                               | Request timeout (ms)      |

---

# ☁️ Production Deployment

A production-ready deployment can be achieved using the following architecture:

```text
                Users
                  │
                  ▼
          CloudFront CDN
                  │
                  ▼
        React Static Website
             (Amazon S3)
                  │
                  ▼
         Application Load Balancer
                  │
                  ▼
      ECS Fargate (Express Backend)
                  │
                  ▼
          MongoDB Atlas Cluster
```

### Infrastructure

* Frontend hosted on **Amazon S3**
* Global caching using **CloudFront**
* Backend deployed on **AWS ECS Fargate**
* Persistent storage using **MongoDB Atlas**
* Infrastructure managed through **Terraform**

---

## Example Terraform (Backend ECS Task)

```hcl
resource "aws_ecs_cluster" "uptime_cluster" {
  name = "uptime-monitoring-cluster"
}

resource "aws_ecs_task_definition" "backend_task" {
  family                   = "uptime-backend"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]

  cpu    = "256"
  memory = "512"

  execution_role_arn = aws_iam_role.ecs_execution_role.arn

  container_definitions = jsonencode([{
    name      = "uptime-backend"
    image     = "123456789012.dkr.ecr.us-east-1.amazonaws.com/uptime-backend:latest"
    essential = true

    portMappings = [{
      containerPort = 5000
      hostPort      = 5000
    }]

    environment = [
      { name = "DATABASE_URL", value = "mongodb+srv://..." },
      { name = "PORT", value = "5000" },
      { name = "CRON_SCHEDULE", value = "*/1 * * * *" },
      { name = "REQUEST_TIMEOUT", value = "10000" }
    ]
  }])
}

resource "aws_ecs_service" "backend_service" {
  name            = "uptime-backend-service"
  cluster         = aws_ecs_cluster.uptime_cluster.id
  task_definition = aws_ecs_task_definition.backend_task.arn

  desired_count = 1
  launch_type   = "FARGATE"

  network_configuration {
    subnets          = ["subnet-123456", "subnet-789012"]
    assign_public_ip = true
    security_groups  = ["sg-0123456"]
  }
}
```

---

# 📖 Documentation

* **README.md** — Project overview and setup
* **AI_LOG.md** — AI collaboration history, prompts, and iterations

---

# 🎯 Future Improvements

* Email & Slack alerts
* SSL certificate validation
* Status page generation
* Authentication
* Historical uptime analytics
* Multi-user workspaces
* WebSocket live updates
* Incident timeline

---

# 📄 License

This project is developed as an educational MVP for demonstrating real-time uptime monitoring using a modern full-stack architecture.
