Validating and mutating admission controller:



![image](https://github.com/user-attachments/assets/f4909abc-18c3-42e1-913a-b4606b143d47)


DefaultStorageClass in your cluster.

So when the PVC is created and you inspect it,

you'll see that a StorageClass default

is added to it

even though you hadn't specified it during the creation.

So this type of admission controller

is known as a mutating admission controller.

It can change or mutate the object itself

before it is created.



what if we want our own admission controller

with our own mutations

and validations that has our own logic?

To support external admission controllers,

there are two special admission controllers available,

MutatingAdmissionWebhook,

and then ValidatingAdmissionWebhook.



![image](https://github.com/user-attachments/assets/2dd71e0f-8288-4168-a7d9-a8f9c3fb8f32)




Admission webhook server :

First, we must deploy our admission webhook server,

which will have our own logic,

and then we configure the webhook on Kubernetes

by creating a webhook configuration object.



![image](https://github.com/user-attachments/assets/84b83b44-0ba7-434d-b30a-e8ca1a8f73d7)




![image](https://github.com/user-attachments/assets/8766bbde-3b3e-47bd-8bc7-c8f2bc4e0ac3)




Create TLS secret webhook-server-tls for secure webhook communication in webhook-demo namespace.



We have already created below cert and key for webhook server which should be used to create secret.

Certificate : /root/keys/webhook-server-tls.crt

Key : /root/keys/webhook-server-tls.key







``` kubectl create secret tls webhook-server-tls \
  --cert=/root/keys/webhook-server-tls.crt \
  --key=/root/keys/webhook-server-tls.key \
  --namespace=webhook-demo ```
