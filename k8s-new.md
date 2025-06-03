Display the history of revisions made to the brezyweather deployment.

``` kubectl rollout history deployment/brezyweather-deployment ```


Roll back the deployment to the previous revision. The deployment gets rolled back.

``` kubectl rollout undo deployment brezyweather-deployment ```

You can also undo the deployment to a specific revision. Refer to the Rolling Back to a Previous Revision article to learn more.


Display the status of the deployment. The deployment successfully rolled out message gets displayed.

``` kubectl rollout status deployment/brezyweather-deployment ```


View the details of the brezyweather deployment. The image is set to codewithpraveen/labs-k8s-rolling-update:1.0 and revision number 3.

``` kubectl describe deployment brezyweather-deployment ```


Check the running pods. The details of the pods are shown with the status of Running. Note down the name of the first pod to Notepad or TextEdit.

kubectl get pods


View details of any of the pods. Replace xxx with the pod name you copied in the previous step. Note down the IP address under the IP field.

``` kubectl describe pod xxx ```


Test the GET API endpoint in v2. Replace xxx with the IP address noted in the previous step. The result shows an unsupported API version, which had been rolled back.

``` curl xxx:80/api/v2/weather ```

``` kubectl get event ```
