# AWS INSPECTOR

![image](https://github.com/user-attachments/assets/7dcb3162-80ec-48dd-bbfa-de3461337ca2)

In this example we have allows ports like 21,23,and 28 which is not required, But allowed to check how INSPECTOR IS WORKING.

Steps :
1. We need to install INSPECTOR AGENT in EC2 instance. (Backend)
```
https://docs.aws.amazon.com/inspector/v1/userguide/inspector_installing-uninstalling-agents.html
```
```
wget https://inspector-agent.amazonaws.com/linux/latest/install
curl -O https://inspector-agent.amazonaws.com/linux/latest/install
```
Install it 
--
```
sudo bash install
```
2. Open aws inspector
- ![image](https://github.com/user-attachments/assets/10268514-4989-4e43-8706-cee3f1e43b6b)
- Select classic inspector
- Click on get started
- cancel it ![image](https://github.com/user-attachments/assets/ebbcd381-9310-42d0-98aa-906f6a740781)
- We need to create Assessment Targets - For which aws ec2 instance we need to inspect that we need to create as a Assessment Target
- ![image](https://github.com/user-attachments/assets/13448fd6-0560-458a-9a60-cefb57531bab)
- We can include all ec2 instances in that region as well or one ec2 instance

Step 3 :
- We need to create assessment templates
- That are like pre-defined rules
- ![image](https://github.com/user-attachments/assets/e0727232-84fa-4338-90f0-185191f06ab2)
- Here in the rules we are selecting all the rules

Step 4 :
- Select assessment template and run the the assesment
- ![image](https://github.com/user-attachments/assets/17159f55-efa5-488e-b8e6-184b7f0b6327)
- ![image](https://github.com/user-attachments/assets/f2f29a31-57ba-4748-89cf-b989c722ee81)
- ![image](https://github.com/user-attachments/assets/98711eb2-90ac-4688-895b-861113fb8aba)


Step 5:
- Download the report.
- ![image](https://github.com/user-attachments/assets/33f4678d-544f-42e5-b578-ae2db743cf6d)
