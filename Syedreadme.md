
# TravelMemory — Deploying Using EKS cluster


---

   # Create K8s Manifest files for Travelmemory:

      k8s/
      ├── bedeployment.yml  - Backend API Deployment
      ├── beservice.yml     - Backend Service (ClusterIP/NodePort/Loadbalancer depending on config)
      ├── dbdeployment.yml  - MongoDB Deployment
      ├── fedeployment.yml  - Frontend Deployment
      ├── feservice.yml     - Frontend Service( LoadBalanced)
      ├── namespace.yml     - To Creates the namespaces ( For Frontend, Backend and Mongodb)
      ├── pv.yml            - Persistent Volume for MongoDB
      ├── pvc.yml           - Persistent Volume Claim (bound to PV)
      ├── secret.yml        - Stores MongoDB credentials / environment secrets
      └── service.yml       - MongoDB Service

---

   # Frontend Directory Structure

      frontend/
      ├── public/
      │   └── (static assets like index.html, icons, images)
      │
      ├── src/
      │   ├── components/
      │   │   └── (React UI components)
      │   │
      │   ├── App.css
      │   ├── App.js
      │   ├── App.test.js
      │   ├── index.css
      │   ├── index.js
      │   ├── logo.svg
      │   ├── reportWebVitals.js
      │   ├── setupTests.js
      │   └── url.js - # Changes Made
      │
      ├── .gitignore
      ├── Dockerfile - # Need to be created
      ├── nginx.conf
      ├── package-lock.json
      ├── package.json
      └── README.md

    url.js - Changes Made as shown below:

  <img width="677" height="317" alt="image" src="https://github.com/user-attachments/assets/1e42761d-b9cd-4c94-b911-71d51ed51326" />


---


  # Backend Directory Structure:


      backend/
      ├── controllers/
      │   └── (API controller logic)
      │
      ├── models/
      │   └── (Mongoose/MongoDB models)
      │
      ├── routes/
      │   └── (API route definitions)
      │
      ├── conn.js           # MongoDB connection file
      ├── Dockerfile        # Backend container image build file (Need to be created)
      ├── index.js          # Main Express server entry point
      ├── package-lock.json
      └── package.json

      


     





