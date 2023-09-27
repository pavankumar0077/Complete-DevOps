# AWS CLOUD FRONT

### Managed service for CDN
- CDN : Content Delivery Network

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/91d408c0-cb3e-42d1-b84a-fbac0ab84f1e)

Without  CDN
--

- Ex: Instance --> User uploads --> Image --> Instagram will have a Central storage system (Australia)
- Instagram copies the image that we have uploaded --> Now anybody in the world can access the image by using instagarm (Central stroage system)
- A friend of the user --> trying to access the image --> In his instagram account

#

- The user in india (Centeral storage system is in U.S) --> For this person in india to access the image
- The request will go to muliple routers multiple Hawks to reach server and again it goes to multiple Hawks to reach the central storage system)
- From storage system --> Goes to Instagram server --> To User
- HAWKS -- MULTIPLE ROUTERS
- (Available in US) --> The users in US can get the content very fast
- The users in other countries like India they will have loading time Litency is high. UX is not good.

WITH CDN
--
- User uploads an image (US) --> Goes to central storage system --> We will place a CDN here
- People from other parts of world -- anyone trying to access  this image -- Instead of accessing the instagram server directly
- We will map the CDN to the instagram DNS and we will tell to access CDN
- CDN is the service in AWS Cloud front --> Either access my CDN or we will map the domain name DNS with CDN
- Whenever the person in india when they try to access the image they will directly talk to the CDN --> CDN WILL CREATES LOCAL COPIES OF THE IMAGE
- Muliple copies of the image will be stored in the EDGE LOCATIONS (Copy in india region, US, and etc)
- Now user ---> Instead of going to central region we can get the image from india region

- When someone upload a reels or post in instagram --> intagram usese the CDN and it stores the image or reel in multiple local storage locations.
- Instead of image stored in the Central location it will stored on EDGE LOCATIONS.


![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/c1c7f78c-b6b6-4744-844b-d8fe2f7cb3a9)

Create S3 bucket
--
1) s3 bucket -->
2) Block all access --> Should be enabled be'coz we are using direct access to S3 buckets itself to the USER
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/57dc5b42-532a-446e-aec3-7a322f40c253)
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/393c665e-6e09-47de-b865-e55a69560d84)
5) Click on Bucket -> Properties --> Static Website hosting --> Enable it
6) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/b7036860-1f88-429b-a167-dfed70b073b4)
7) Upload any static webpage index.html ``` https://github.com/pavankumar0077/lightweight-gallery/blob/main/index.html ```
8) Go to bucket properties --> In the last you will find URL --> When we try it will show 403 error --> Be'coz we blocked public access

9) NOW WE HAVE TO USE CLOUD FRONT
10) cloudfront --> ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/c604a85c-eabf-4d80-954e-f10d6c21bce4)
11) Click on create new OAI TO ACCESS THE S3
12) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f8978d63-4d29-48f4-9a4a-07e5d09d75a2)
13) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f7127405-ce9a-4ec7-a225-deddf7f7ac81)
14) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/8d9a246f-bf3b-40e1-9a22-2785a95284c6)
15) S3 bucket policy will be added
16) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/c0025e61-0118-4cf9-bc98-16725e3033ab)
17) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/23e7c688-25c4-417b-949e-806d1aded639)
18) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/bc466e32-2372-4123-ac69-0033bd205e0c)
19) CREATE DISTRIBUTION
20) If we copy and check distributed domain name
21) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/0845cfdc-a907-4db0-a425-47ceb0ce769b)

