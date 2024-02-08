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

Apply the deployment using:
- kubectl apply -f deployment.yml

Autoscale the Guestbook deployment:
- kubectl autoscale deployment guestbook --cpu-percent=5 --min=1 --max=10

To check the current status of the newly-made HorizontalPodAutoscaler, by running:
- kubectl get hpa guestbook

Enter the below command to generate load on the app to observe the autoscaling:
- kubectl run -i --tty load-generator --rm --image=busybox:1.36.0 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- <your app URL>; done"

Run the below command to observe the replicas increase in accordance with the autoscaling:
- kubectl get hpa guestbook --watch


