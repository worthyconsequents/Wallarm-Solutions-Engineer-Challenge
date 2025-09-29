# Wallarm Solutions Engineer Technical Evaluation Solution

## 📌 Overview

Welcome to the **Wallarm Solutions Engineer Technical Evaluation**. This exercise is designed to assess your ability to deploy and configure Wallarm's filtering nodes using a deployment method of your choice, troubleshoot any issues encountered, and document your process effectively. Additionally, we will evaluate your ability to leverage our official documentation to complete the task.

---

## 🎯 Objectives

By the end of this evaluation, you should be able to:

✅ Deploy a Wallarm filtering node using a supported method of your choice.  
✅ Configure a backend origin to receive test traffic. (httpbin.org is also acceptable)  
✅ Use the **GoTestWAF** attack simulation tool to generate traffic.  
✅ Document the deployment and troubleshooting process.  
✅ Demonstrate proficiency in using **Wallarm's official documentation**.  

---

## Architecture Overview
Edge Deployment

<img width="792" height="612" alt="WallarmEdge" src="https://github.com/user-attachments/assets/5e5b9c26-1372-4b48-b7f5-0861cbda802f" />

I chose the route to have wallarm act as a reverse proxy to a small web application hosted on an AWS EC2 instance.


Node Deployment

<img width="487" height="403" alt="wallarmdocker2" src="https://github.com/user-attachments/assets/32214b52-99dc-43be-9841-00e2d8f2f648" />

This is the network diagram for how the traffic will flow from user to the web application with a Node. The Node will communicate with the wallarm console located on seperate infrastructure.

## Wallarm Edge Deployment

Step 1: Accessing to the Wallarm UI

I went into the email sent from Wallarm SE to get access to the console (https://**.audit.****.com) and followed the steps to get my console up and running

Step 2: Setup Edge deployment

I followed the instructions to ensure my traffic was routed via DNS to Wallarm (https://docs.wallarm.com/installation/security-edge/inline/overview/)
<img width="2488" height="672" alt="image" src="https://github.com/user-attachments/assets/5e3890aa-30e9-4ff0-8adc-b4725cefc2f3" />
<img width="2546" height="438" alt="image" src="https://github.com/user-attachments/assets/27e4c385-b10c-4bd3-8249-17b6af7f5b5f" />

## Edge Deployment Wallarm GoTestWAF 
Reqs: Docker Installed on workstation

Step1: Run GoTestWAF

docker run --rm --network="host" -it -v ${PWD}/reports:/app/reports \
    wallarm/gotestwaf --url=http://example.com

Step2: Confirm Attacks were filtered by Wallarm console
<img width="3328" height="1758" alt="image" src="https://github.com/user-attachments/assets/caae7180-d3c1-4f15-8257-58feee52c938" />

## Wallarm Node Deployment
Step 0: Deploy your Web Application

In my case, I deployed httpbin with the following command. I picked port 8081 due to the fact port 80 is used by the wallarm node.


docker run -p 8081:80 kennethreitz/httpbin 

Step 1: Generate Node Token

I generated a Node Token within the Wallarm console.

Step 2: Deploy Wallarm Docker Node


docker run -d -e WALLARM_API_TOKEN='<YOUR TOKEN>' -e WALLARM_LABELS='group=docker01' -e NGINX_BACKEND='localhost:8081' -e WALLARM_API_HOST='****.api.wallarm.com' -e WALLARM_MODE='block' -p 80:80 wallarm/node:6.5.1

Step 3: Confirm Node sync
<img width="1463" height="335" alt="wallarmNodeSynced" src="https://github.com/user-attachments/assets/1f5f1a90-aa98-42aa-ad07-3acd79ad2a19" />

Step 4: Sanity Check

Confirm traversal attack
<img width="1477" height="250" alt="wallarmSanitycheck" src="https://github.com/user-attachments/assets/64871350-ccc2-4703-b7b3-b698d244436e" />

## Node Deployment Wallarm GoTestWAF 
Step 1: Run GoTestWAF direct


Run a test attack directly to the server without the filtering node.


docker run --rm --network="host" -it -v ${PWD}/reports:/app/reports \
    wallarm/gotestwaf --url=localhost:8081 --blockStatusCodes 200

Step 2: Run GoTestWAF

Run a test in front of the filtering Node.


docker run --rm --network="host" -it -v ${PWD}/reports:/app/reports \
    wallarm/gotestwaf --url=localhost:80

Step 3: Confirm attacks on console
<img width="1699" height="822" alt="wallarmAttacksDashboard" src="https://github.com/user-attachments/assets/d1ea1a5e-124c-41e9-983a-8651dab6fada" />

Step4: Review Results

Without filtering node
<img width="1228" height="711" alt="GoTestWAF_noWAF" src="https://github.com/user-attachments/assets/63e89f34-8afb-4def-a412-9f18b721addb" />

With filtering node
<img width="1238" height="699" alt="GoTestWAF_WAF" src="https://github.com/user-attachments/assets/5c31e883-b578-46dc-80f6-ccb5a727c49e" />


## Notes
Documentation was from the official documentation
https://docs.wallarm.com

Full GoTestWAF reports are uploaded to github
Console with attacks are uploaded to github

Errors
- Encountered the no WAF detected error, but was solved as I was limiting traffic to the EC2 instance from a specific IP
<img width="2894" height="290" alt="image" src="https://github.com/user-attachments/assets/08c93101-de57-4c94-8255-b261050d07a4" />

- Port 80 is being used by wallarm node clashed with web application, changed port web application was using.
- Issue getting the Node to register, following the docs had API host being the wallarm production tenant which was solved by pointing to the proper tenant.

## Closing Thoughts
It was very fun to deploy a filtering Node. It was fun to run the GoTestWAF tool and see the attacks coming into the application.


📌 Completeness: Were all required tasks completed? ✅ Yes
📌 Clarity: Is the documentation clear and well-structured? ✅ Yes
📌 Troubleshooting: How well did you document and resolve any issues? ✅ Yes
📌 Understanding of the Product: Did you correctly set up and use the Wallarm filtering node? ✅ Yes
📌 Use of Official Documentation: Did you successfully leverage Wallarm's official resources? ✅ Yes


