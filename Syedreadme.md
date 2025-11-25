
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

---

# Build Dockerfile and push to dockerhub (Frontend and backend):

      docker login (Autheticate you dockerhub)
      
      docker build -t <dockerhubusername/imagename> .

      Docker push <dockerhubusername/imagename>

   Once pushed the images to dockerhub, Please replace the image names in fromtend and backend yaml files
      
   <img width="692" height="687" alt="image" src="https://github.com/user-attachments/assets/49882f4e-d049-4731-8a00-ccce29c8f500" />

   <img width="788" height="707" alt="image" src="https://github.com/user-attachments/assets/f9d4c1ab-bd81-4dbc-b0c5-5ef4f20cb390" />


# Deploy the yaml files:

# 1.Once all pods are running in respictive namespaces.

        kubectl apply -f .

# 2.Once service.yml deployed for both frontend and backend (Loadbalanced created in AWS )

        kubectl get svc -n tmb13fe
      
   <img width="1382" height="73" alt="image" src="https://github.com/user-attachments/assets/6334f3a4-ba12-456c-9b90-266ad00f66f0" />
   

        kubectl get svc -n tmb13be

   <img width="1336" height="87" alt="image" src="https://github.com/user-attachments/assets/ade96b89-23ba-4cb1-b8b7-0f993eba885a" />

   

# 3.Add this backend loadbalancer DNS in fedeployment.yml file 

   <img width="1567" height="470" alt="image" src="https://github.com/user-attachments/assets/e58798a7-c56e-4aa6-b6c0-7bdccd801450" />




# 4.Navigate to AWS loadbalancer

   <img width="1907" height="482" alt="image" src="https://github.com/user-attachments/assets/ac151df2-4952-4184-8ff8-1fd67ffc87f2" />

   

 # 5.Open frontend loadbalancer --> security group ---> Edit inbound rules--> add 3001 TCP port to allow backend service

   <img width="1855" height="722" alt="image" src="https://github.com/user-attachments/assets/9bbc923c-f875-4c0c-a918-1a248161588c" />

   <img width="1876" height="715" alt="image" src="https://github.com/user-attachments/assets/e279b0cc-d127-43ba-96cd-f76cdf6e63ad" />

   

 # 6.Open backend loadbalancer --> security group ---> Edit inbound rules--> add 3000 TCP port to allow frontend service

   <img width="1878" height="726" alt="image" src="https://github.com/user-attachments/assets/570c052a-114f-48be-ad93-001378b89216" />

   <img width="1872" height="703" alt="image" src="https://github.com/user-attachments/assets/ce853ae0-1472-4abc-9b4e-37d33bee453c" />

# Outcome:

  Use frontend loadbalncer DNS to access the travelmemory Application

<img width="1358" height="78" alt="image" src="https://github.com/user-attachments/assets/47c69b36-22d9-4e2c-b812-492c398afb27" />


<img width="1171" height="778" alt="image" src="https://github.com/user-attachments/assets/89a01d1f-cbbd-4ebf-b6d9-629f874e69fa" />







   

   





   






        


      
            


     





