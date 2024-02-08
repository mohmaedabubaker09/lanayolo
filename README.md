# LanaBot: An AI-Powered Telegram Bot for Text Chat, Object Detection, and Image Generation

## Overview
LanaBot is an advanced Telegram bot driven by AI, enhancing user interactions with intelligent text responses, sophisticated image analysis, and creative image generation. By harnessing the capabilities of OpenAI's GPT models for text interactions, YOLOv5 for image object detection, and DALL-E for image generation, LanaBot offers users an immersive experience. Hosted on an AWS EKS Kubernetes cluster, LanaBot ensures scalability, security, reliability, and peak performance. Comprising two microservices that employ an asynchronous architecture utilizing SQS FIFO for message queuing and DynamoDB for state storage, LanaBot maintains robust CI/CD pipelines using GitHub, Jenkins for CI (master-slave setup), and Argo CD for CD. Additionally, continuous monitoring with Grafana integrated within the cluster ensures optimal system health.

## Key Features
- AI-Based Text Interactions: Engage in intelligent conversations powered by OpenAI's GPT models.
- Image Analysis and Object Detection: Utilize YOLOv5 for advanced object detection in uploaded images.
- Creative Image Generation: Leverage DALL-E for imaginative image creation based on detected objects.
- Scalable and Resilient Architecture: Hosted on AWS EKS with load balancing, auto-scaling, and high availability features.

## Architecture
**AWS Ecosystem Integration**: LanaBot seamlessly integrates with various AWS services including ALB, Secrets Manager, ECR, EC2, S3, SQS FIFO, DynamoDB, Lambda, EventBridge, CloudWatch, and Auto Scaling Group, ensuring efficiency in scaling and managing variable workloads.

**Core Components**:
- EC2 Instances: Host the Telegram bot and YOLO object detection services.
- Docker Containers: Ensure consistent environments across services.
- SQS FIFO: Handles message queues between services.
- DynamoDB: Stores YOLO prediction results.
