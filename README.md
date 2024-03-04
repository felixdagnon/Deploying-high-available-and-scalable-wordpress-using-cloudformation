  Implement-high-available-and-scalable-wordpress-using-cloudformation with EC2, Application Load Balancing, MySQL RDS
  

## 1-Introduction

Let see this diagram.

![image](https://github.com/felixdagnon/Implement-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/56f02578-57d4-4bc3-8b38-7f1fad471b8b)

WordPress is a web server running on EC2. 

Now we have two options.

One is we manage the EC2 ourself. What do I mean by that?

So we can spin up to EC2 in two different availability zones. Because this makes the application highly available.

We also need to make sure it's scalable. So we need to create a auto scaling group and attach it to these EC2.

And finally, we need to expose these EC2 because there are multiple EC2, we need to have a way to distribute traffic as well as expose this web server application using a URL. So we need to create an elastic load balancer.

We'll have the URL which will be expose to the internet and users can access it.

We need to expose this url through load balancer as well as we secured it using https SSL certificates.

We can do all this by ourself or we can use Elastic Beanstalk.

Elastic Beanstalk takes care of this auto scaling, managing the web servers, etc..

We need to have some way to store all information in the page, links, etc..

So that's why this Amazon RDS MySQL database is used.

Why there are two instances? because we want to make it highly available. This is a multi A-Z instance of the database.

The primary instance is running in availability zone A The multi AZ replicate in availability zone B 

So even if AZ A goes down,  the standby database will become the primary and it will fail over.

And because we have load balancer will do health check, if the whole availability zone goes down, the traffic will be shifted to the availability zone B.

Now what is this S3 bucket? This S3 bucket is created when you provisioning elastic beanstalk application.

It creates S3 bucket to save our code, log files, etc. If I'm not using elastic beanstalk, then I do not need this S3 bucket.


I want to show alteranative concepts. Instead, I want to show how to provision this without elastic beanstalk using infrastructure as code.


## 2-Cloudformation

Connect in Cloudformation console to create stack

Chose "use a sample of template"

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/098698a2-09d2-4708-a5a7-241e5f9250ca)

Select WordPress Blog " The template installs high-available scalable WordPress deployemnt using using a multi-az Amazon RDS Database instance for storage"

Click "View in Designer"

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/78988801-6c44-4061-87fa-1cca652d4f05)

Then click create stack icon

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/35810e82-bcdf-413e-8b50-bf69ca8a7f91)

The template is ready and Amazon S3 URL is provided an Amazon S3 URL to my template.

Click next

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/5c01c445-7f48-45ab-b108-cd4ed4d62d68)

I give it name "wordpresstest" and select parameters and click next

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/b27cbd00-20ec-4f54-9375-1f7804a5074c)

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/2ae03dbc-da9c-4ad0-b53e-1d77f1432e7b)


Keep "Configure stack options" default and click next

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/49495679-04e7-4af3-983a-89669913b4c6)

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/4fa40b3f-3612-4c82-a0bd-90a43e80e8a0)

Keep "Review and create" default and click submit. This is trigger Cloudformation to run and create aws resources

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/98e06251-01db-4cc7-af76-00f2c9d0ac70)

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/9e2da0ec-d839-4c80-b2dc-3e6f5c445d59)

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/d496b2fb-2c06-4d9f-a3cb-86f7947899f6)

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/1807a6cb-e2b0-4ec8-a2da-436fd011e7d0)

Creating Cloudformation stack in progress.It's running and creating aws resources.it created the database instance, launch config, etc..

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/756eb840-99a9-4636-9914-4422c13415a3)

All resouces are completed

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/df2677c9-f25b-4afa-b55b-d6b095ebd4d4)

So let's go check out the RDS database as well as the EC2 and then we are going to access this web server.

if I go to EC2, we should see an instance running. This is the instance that's running t2micro.

And if I go to the tags so it shows the tags and, and you know, this is created using our cloudformation stack "wordpresstest".

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/3f151e87-cbd4-4b61-933d-e7013ad1ebe3)

Let's check MySQL RDS. It's running. So it shows the CPU status is available.

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/0c09f8dd-7e95-432c-8985-899dbac50323)

So it's running in two availability zone one is us-east-1f, two D And then under configuration the secondary zone is us-east-1c

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/8444219d-38ed-483b-958f-3d9fef4cab20)

So now let's go back to cloudformation and access our WordPress server cloud formation, go to outputs.

This is the URL, so let's click this"http://wordpr-Appli-5KWqbkyULNi8-199131677.us-east-1.elb.amazonaws.com/wordpres"

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/8cedb5df-8ec6-4e07-9f52-67bbc66ce177)

So this is the site title. Sample WordPress. 

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/60cd1d9d-cd7d-4b13-b88b-610f5bf951ab)

Click install WordPress

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/9207357d-16ec-440e-93ce-e5041c43e9a5)

Click log in.

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/35886aa6-494e-4d52-9bf5-971008cbd94c)

Here is the wordpress dashboard

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/dfefb223-8d03-4cd1-a9f6-70be4f670925)


By default, the admin console will open up and from here you can add a new page. We can add content to our page, and then we can publish the page.

Add a neww page

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/06411c7a-b388-4aae-8f1b-c844106fa965)

I am not WordPress expert, but let me create my resume in WordPress!

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/44b8432f-3959-4d82-803e-7e03956354e3)

So let's publish this. Page published.

Let's click View Page.

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/3b991b90-d64d-4b21-a9df-09f258d4fb80)

So this is our sample page. So you could see Felix DAGNON here. You can click "Felix DAGNON Bio" and it's going to open this page.

And remember that the URL is the URL of the application load balancer that is forwarding the traffic to the EC2 running the WordPress.

![image](https://github.com/felixdagnon/Deploying-high-available-and-scalable-wordpress-using-cloudformation/assets/91665833/21541606-6a2d-495b-977f-dca67acbfed5)

## 3-Conclusion

So with that being said, there are two ways to implemente our WordPress. Elastic Beanstalk and Infrasture as code.



















































