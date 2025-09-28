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
I chose the route to have wallarm act as a reverse proxy to a small web application hosted on an AWS EC2 instance.
<img width="792" height="612" alt="WallarmEdge" src="https://github.com/user-attachments/assets/5e5b9c26-1372-4b48-b7f5-0861cbda802f" />


## Wallarm Edge Deployment

Step 1: Accessing to the Wallarm UI
I went into the email sent from Wallarm SE to get access to the console (https://**.audit.****.com) and followed the steps to get my console up and running

Step 2: Setup Edge deployment
I followed the instructions to ensure my traffic was routed via DNS to Wallarm (https://docs.wallarm.com/installation/security-edge/inline/overview/)
<img width="2488" height="672" alt="image" src="https://github.com/user-attachments/assets/5e3890aa-30e9-4ff0-8adc-b4725cefc2f3" />
<img width="2546" height="438" alt="image" src="https://github.com/user-attachments/assets/27e4c385-b10c-4bd3-8249-17b6af7f5b5f" />

## Wallarm GoTestWAF
Reqs: Docker Installed on workstation

Step1: Run GoTestWAF
docker run --rm --network="host" -it -v ${PWD}/reports:/app/reports \
    wallarm/gotestwaf --url=http://example.com

Step2: Confirm Attacks were filtered by Wallarm console
<img width="3328" height="1758" alt="image" src="https://github.com/user-attachments/assets/caae7180-d3c1-4f15-8257-58feee52c938" />

## Notes
Encountered the no WAF detected error, but was solved I was limiting traffic to the EC2 instance
<img width="2894" height="290" alt="image" src="https://github.com/user-attachments/assets/08c93101-de57-4c94-8255-b261050d07a4" />

Originally, I wanted to deploy this natively via docker image, but I was unsuccessful in having the Node properly register to the Wallarm console. I checked the API tokens/Node tokens and was not able to resolve this. I checked yaml files and logs to see if I could resolve, but was stuck on the acl folder path.
<img width="1094" height="1044" alt="image" src="https://github.com/user-attachments/assets/82bb8b25-f359-49e8-9b86-854f6a1654b0" />

<img width="1688" height="1700" alt="image" src="https://github.com/user-attachments/assets/9167f783-d6ec-496c-9ab0-5d94a477ee7b" />

## Closing Thoughts
It was very fun to deploy a filtering Node. It was fun to run the GoTestWAF tool and see the attacks coming into the application.



