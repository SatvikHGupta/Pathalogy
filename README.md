# 🧪 Pathology Lab – React + Vite Application

A modern and production-ready **Pathology Lab frontend application** built using **React 19 and Vite**, styled with **Tailwind CSS**, and deployed using **Docker, Nginx, Jenkins CI/CD, and GitHub Actions**.


---

## 🚀 Tech Stack

### Frontend

* React 19
* Vite
* Tailwind CSS
* Framer Motion
* Lucide React Icons
* React Hot Toast
* date-fns

### Tooling & Quality

* ESLint
* npm ci (clean & fast installs)

### DevOps & Deployment

* Docker (Multi-stage build)
* Nginx (Production web server)
* Jenkins CI/CD pipeline
* Trivy security scanning
* GitHub Actions

---

## 📂 Project Structure

```
pathology-lab/
│
├── .github/workflows/      # GitHub Actions CI
├── public/                 # Static assets
├── src/
│   ├── assets/             # Images & static files
│   ├── components/         # Reusable React components
│   ├── App.jsx
│   ├── App.css
│   ├── index.css
│   └── main.jsx
│
├── Dockerfile               # Multi-stage Docker build
├── Jenkinsfile              # Jenkins CI/CD pipeline
├── index.html               # Vite entry file
├── vite.config.js
├── eslint.config.js
├── package.json
├── package-lock.json
└── README.md
```

---

## 🐳 Docker Setup

This project uses a **multi-stage Docker build** for optimized production deployment.

### Build Stage

* Base image: `node:18-alpine`
* Installs dependencies using `npm ci`
* Builds optimized static files

### Production Stage

* Base image: `nginx:alpine`
* Serves the build files via Nginx
* Lightweight and secure image

---

## ▶️ Run Locally

### 1. Clone the repository

```bash
git clone https://github.com/SatvikHGupta/Pathalogy.git
cd pathology-lab
```

### 2. Install dependencies

```bash
npm install
```

### 3. Start development server

```bash
npm run dev
```

Application runs on:

```
http://localhost:5173
```

---

## 🏗 Build for Production

```bash
npm run build
```

The optimized build output will be generated in the `dist/` directory.

---

## 🐳 Run with Docker

### Build Docker Image

```bash
docker build -t pathology-lab .
```

### Run Container

```bash
docker run -d -p 80:80 pathology-lab
```

Open in browser:

```
http://localhost
```

---

## 🔁 CI/CD Pipeline

### Jenkins Pipeline

* Automated build
* Docker image creation
* Trivy vulnerability scanning
* Production-ready workflow

### GitHub Actions

* Continuous Integration
* Code quality and build checks on push

---

## 🔐 Security

* Trivy vulnerability scanning
* Clean dependency installation using `npm ci`
* Minimal production image with Nginx

---

## 🎯 Key Features

* Modern React architecture
* Responsive UI with animations
* Scalable component structure
* Production-grade Docker setup
* CI/CD enabled workflow
* Security scanning integrated

---

## 📌 Use Cases

* Portfolio project (Frontend + DevOps)
* React + Docker practice
* CI/CD pipeline demonstration
* Production-ready static web application

---
