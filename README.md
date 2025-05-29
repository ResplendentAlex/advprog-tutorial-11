# advprog-tutorial-11
Alexander William Lim  
2306207505  
AdvProg A

## Reflection on Hello Minikube
 1. Compare the application logs before and after you exposed it as a Service.
    ### Logs before Running Service
    ![Logs Before Runnning Service](https://github.com/user-attachments/assets/bb7b59b8-3595-4c6d-9ee9-394cc5ba0c7f)  
    ### Logs after Running Service
    ![Logs After Running Service](https://github.com/user-attachments/assets/4c5e71af-0b0c-4875-98ac-1c20b6b89f18)  
    Comparing the two images above, we can see that there new logs added once the server is run. All of these logs shows the request GET/. This happens when I try to open the service page hosted locally. Since I am trying fetch the page, the service detects this as an attempt to access the page through a HTTP GET request, thus the logs present.

2. What is the purpose of the `-n` option on the second invocation of `kubectl get` and why did the output not list the pods/services that you explicitly created?
   The `-n` option represents the word namespace. This option allows us to choose specific namespaces that are saved in our service. In this case, `kubectl get` accompanied with the invocation `-n kube-system` allow us to get only pods/services that are registered under kube-system. This means that other pods/services, unless explicitly registered under the same namespace, will not be shown when we invoke the instruction. Because of this, we would not able to see the pods/services that we have explicityl created.

## Reflection on Rolling Update & Kubernetes Manifest File
1.  What is the difference between Rolling Update and Recreate deployment strategy?
    ### Rolling Update Deployment
    In the Rolling Update deployment strategy, the deployment updates the Pods in a rolling update fashion, where it is possible to specify the `maxUnavailable` and `maxSurge` to control the update process. With the `maxUnavailable` parameter, we can specify how many Pods can be deactivated at a time, while the `maxSurge` is used to specify the number of Pods that can be created over the desired number of Pods. For every Pods that are made unavailable, the deployment would first prioritize on updating those pods before proceeding with the rest of the pods. The process continue until each pods have been successfully updated.

    ### Recreate Deployment
    In the Recreate deployment strategy, the deployment first kill all existing Pods before new ones are recreated. This means that there will be new pods that are added to the deployment before each and every one of the previous pods have been removed from the deployment.

    ### Differences
    As we can see from the two deployment strategy's description, while the rolling update deployment strategy gradually updates the pods with set bounds, the recreate deployment strategy completely kills all existing pods before updates on the pods are added by recreating them.

2. Deploying Spring Petclinic REST using Recreate deployment strategy
   To deploy Spring Petclinic REST using the Recreate deployment strategy, we first need to change the `.spec` configuration of the deployment. This could be done by accessing the `.spec` file through the vim editor with this command
   ```bash
   kubectl edit deployment <deployment-name>
   ```
   `<deployment-name>` depends on the name of the deployment that have been created. In my case `<deployment-name>` would be replaced with spring-petclinic-rest. Once we have accessed the file, we should change `.spec.strategy` configuration to this:
   ![Code Specification](https://github.com/user-attachments/assets/69cc020e-f4fa-4aeb-a006-d199fdaab4da)
   
   Here, I am changing the configuration for the strategy so that it would use Recreate instead of the default Rolling Update deployment strategy. Next, I would like to update my spring-petclinic-rest to the latest image, so that there would be obvious differences. This is the current state of my pods and deployments:
   
   a) `kubectl get deployments`
   ![Before Update Deployments](https://github.com/user-attachments/assets/becfc5ee-682c-4a5a-9abb-83d119b7fc48)
   
   b) `kubectl get rs`
   ![Before Update rs](https://github.com/user-attachments/assets/605dcbc1-e167-4b26-b2e1-ebde69e9a783)
   
   c) `kubectl get pods -o wide`
   ![Before Update pods](https://github.com/user-attachments/assets/67640dad-4e79-4c72-bbd1-e7a25b5fce72)
   
   d) `kubectl describe pods <pod>`
   ![Before Update pods description](https://github.com/user-attachments/assets/77f0786c-d239-45ee-b0fc-50d925d633aa)

   After changing the current image to the Docker image link `docker.io/spring-petclinic-rest:latest` which would replace it with the working latest image registered in Docker Hub. This could be seen from the change in the description of the deployment:
   
   a) `kubectl describe deployment/spring-petclinic-rest`
   ![After Update Deployment](https://github.com/user-attachments/assets/3458f4b2-88f4-426b-b76b-f951778bed4f)
   
   b) `kubectl get pods -o wide`
   ![After Update pods](https://github.com/user-attachments/assets/29210b2d-4b67-42cc-bf76-d3f5a267c843)

   As we can see from the images above, there are events that shows the replica set being scaled all the way down to 0, and then rescaled back up to 4. This is exactly the same as killing all the existing pods and recreating it to implement the new update - which is the very definition of the Recreate deployment strategy. It is also clear in the image that all the pods all has the same age, a further indication that all four running pods are created at the same time.

4. What do you think are the benefits of using Kubernetes manifest files? Recall your experience in deploying the app manually and compare it to your experience when deploying the same app by applying the manifest files (i.e., invoking `kubectl apply-f` command) to the cluster

   One thing that I can say for sure is that the manifest files allow for a faster setup. Previously, when I need to deploy a specific deployment with set configurations, I need to manually set them, as well as create the deployment from scratch again. This would definitely take way more time compared to when I only need to invoke a single command to recreate the deployment I wish to have. Not only that, I think that the manifest files could also serve as some sort of version control, where I can choose certain deployments that are more suited to some situations, and then applying it to the current deployment.
