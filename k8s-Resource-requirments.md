![image](https://github.com/user-attachments/assets/f4172a7d-7699-4b70-88c2-4a5b6eb3badf)

![image](https://github.com/user-attachments/assets/3c91b438-115c-4ae4-a8a2-e83968cf7ca6)


If nodes have no sufficient resources available,
the scheduler avoids placing the pod on those nodes
and instead places the pod
on one where sufficient resources are available.
And if there is no sufficient resources available
on any of the nodes,

![image](https://github.com/user-attachments/assets/5e9dd3b7-262c-42ed-aae5-379010f9adb2)


![image](https://github.com/user-attachments/assets/18d83d00-0f5b-45ae-8fdc-372044ce47bc)


A container cannot use more CPU resources than its limit.
However, this is not the case with memory.
A container can use more memory resources than its limit.
So if a pod tries to consume
more memory than its limit constantly,
the pod will be terminated
and you'll see that the pod terminated
with an OOM error in the logs
or in the output of the described command when you run it.
So that's what is OOM refers to out of memory kill.

![image](https://github.com/user-attachments/assets/61f5a713-a11d-4f8d-8cab-0c651e35b347)


![image](https://github.com/user-attachments/assets/87b59c50-a841-4f4a-a40f-dcef139cc4f5)

So a resource quota is a namespace level object
that can be created to set hard limits
for requests and limits.

![image](https://github.com/user-attachments/assets/7dc3bcc3-000b-4e17-a433-e55a89b654d2)
