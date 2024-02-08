# Build-and-Deploy-Simple-Guestbook-App

Guestbook is a simple web application that I have build and deployed with Docker and Kubernetes. The application consists of a web front end that will have a text input where one can enter any text and submit it. For all of these, I have created Kubernetes Deployments and Pods. Then I applied Horizontal Pod Scaling to the Guestbook application and finally worked on Rolling Updates and Rollbacks.

I exported my namespace as an environment variable so that it could be used in subsequent commands:
- export MY_NAMESPACE=sn-labs-$USERNAME

Build the guestbook app using the Docker Build command:
- docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1

Push the image to IBM Cloud Container Registry:
- docker push us.icr.io/$MY_NAMESPACE/guestbook:v1

Verify that the image was pushed successfully:
- ibmcloud cr images
- View crimages.png

Apply the deployment using:
- kubectl apply -f deployment.yml

Open a New Terminal and enter the below command to view your application:
- kubectl port-forward deployment.apps/guestbook 3000:3000
- View app.png

Autoscale the Guestbook deployment:
- kubectl autoscale deployment guestbook --cpu-percent=5 --min=1 --max=10

To check the current status of the newly-made HorizontalPodAutoscaler, by running:
- kubectl get hpa guestbook
- View hpa.png

Enter the below command to generate load on the app to observe the autoscaling:
- kubectl run -i --tty load-generator --rm --image=busybox:1.36.0 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- https://ayusshgupta2-3000.theiaopenshiftnext-0-labs-prod-theiaopenshift-4-tor01.proxy.cognitiveclass.ai/; done"

Run the below command to observe the replicas increase in accordance with the autoscaling:
- kubectl get hpa guestbook --watch
- View hpa2.png

To perform Rolling Updates and Rollbacks on the Guestbook application, follow the below steps:
1) Update the title and header in index.html to any other suitable title and header like <Your name> Guestbook - v2 & Guestbook - v2.

   Run the below command to build and push your updated app image:
   - docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1 && docker push us.icr.io/$MY_NAMESPACE/guestbook:v1

2) Update the values of the CPU in the deployment.yml to cpu: 5m and cpu: 2m as below:

   Apply the changes to the deployment.yml file:
   - kubectl apply -f deployment.yml
   - View deployment.yml

3) Open a new terminal and run the port-forward command again to start the app:
   - kubectl port-forward deployment.apps/guestbook 3000:3000
   - View up-app.png

4) Run the below command to see the details of Revision of the deployment rollout:
   - kubectl rollout history deployments guestbook --revision=2
   - View rev.png

5) Run the below command to get the replica sets and observe the deployment that is being used now:
   - kubectl get rs

6) Run the below command to undo the deploymnent and set it to Revision 1:
   - kubectl rollout undo deployment/guestbook --to-revision=1

7) Run the below command to get the replica sets after the Rollout has been undone. The deployment being used would have changed as below:
   - kubectl get rs
   - View rs.png


