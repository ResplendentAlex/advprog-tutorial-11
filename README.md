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
