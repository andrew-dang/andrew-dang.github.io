---
title: "Lessons Learned - Real Estate Comparison Dashboard"
categories:
  - blog
layout: single
classes: wide
tags:
  - real-estate
  - Plotly
  - Dash
  - GCP
  - dashboard
---
[![](https://img.shields.io/badge/Blog-Read%20Article-blue?logo=blogger)](../blog/real-estate-dash/) [![](https://img.shields.io/badge/Google_Cloud_Run-Go_to_app-blue?logo=googlecloud)](https://dash-app-ctyqyt7wvq-uc.a.run.app/)

# About the Project
I built a dashboard to assess whether a real estate listing was priced fairly based on comparable homes available on the market. The dashboard was built using Plotly and Dash, and it was deployed as a web application using Google Cloud Run. I had built dashboards with Plotly and Dash before, but these dashboards were connected to local CSV files as its data source. However, for this project, I wanted to emulate a production environment where data is being loaded into a database on a regular schedule, and the dashboard could display the fresh data. The data sources in my previous projects were updated with data pipelines that were orchestrated locally, which did not run when my computer was asleep or powered down. 

I knew that I could overcome these issues with cloud resources. I could spin up a relational database on the cloud, and I could provision a virtual machine on the cloud that would run 24/7, ensuring my pipelines were always online. In my head, using cloud resources to complete the project seemed simple enough. However I did face a few challenges and many lessons were learned from this project. Below are my key learnings. 

# The Joys of Provisioning and Connecting IT Infrastructure
Provisioning IT infrastructure on the cloud is quite simple. You can click through the provider's console, use the command line, or even use software like Terraform to provision and manage your IT resources. What I had not anticipated was figuring out how to connect my various IT resources so that the system could function without any hiccups. In addition to provisioning my cloud resources, below is a short task list of what I needed to do in order allow the various components in my system to communicate with one another:
- Configure firewall rules
- Create and manage service accounts with varying permissions and levels of access (following the principle of least privilege!) 
- Install binaries and Python packages on my virtual machines via initialization actions rather than SSH-ing into the VM and using the command line
- Install and use the Cloud SQL Auth Proxy to connect to my Cloud SQL instance

While not a large list, these were tasks that I was fairly unfamiliar with. Something as simple as installing Python packages on my virtual machines became a little trickier when I was spinning up a Dataproc cluster as part of the pipeline rather than having an always-on cluster to reduce my costs. By including cluster creation as part of the data pipeline, I had to ensure that the software was installed during cluster creation rather than connecting to the VM via SSH and installing the required software via the command line. I had to install all my required software using initialization actions, something I had not encountered up to that point. 

Managing service accounts was also something that was relatively new to me. The default service account that is assigned to a Compute Engine instance has limited access, and to access other cloud resources, I needed to provide it with additional permissions. It felt like every time I assigned a new permission, I would encounter a new error. Through an incremental process, the service account eventually had the permissions it needed to communicate with other components in the system. 

In a nutshell, I read through pages of documentation and various blogs to connect all my cloud resources. It was a highly tedious process, but very gratifying once everything was working. I now have a greater appreciation for DevOps, CloudOps, and any other role that is involved in provisioning and connecting cloud infrastructure. 

# Cost Savings - Turn off unused or idle resources!
Even though this project was completed using the free credits offered by GCP, it’s important to keep your costs down unless you want a surprise when your bill arrives. 

GCP provides [billing reports](https://cloud.google.com/billing/docs/how-to/reports) within the Console itself, and is a great way to learn more about your spending. The reports provide impressive granularity. You can view your costs by Project, Service, and even down to the individual SKUs. When investigating my own costs, I noticed that my most costly service was Google Compute Engine. Drilling down a little further, I discovered that this was due to my Dataproc cluster being on 24/7. In reality, the cluster was sitting idle for most of the day, so it was very inefficient and expensive to keep it on all the time. My solution was to delete the always-on cluster, and bake cluster creation and tear down into my data pipeline. This way, my cluster would only be running when it needs to process data, meaning that I would only be billed for the comparatively brief period of time that the cluster is on, instead of the whole day. This drastically reduced my overall costs. 

My other pieces of infrastructure including my database and the virtual machine with Airflow installed needs to be on 24/7 to ensure the system is operational. However, some components sit idle and can be turned off without affecting the service of your system, such as my Dataproc cluster. These are components that are good candidates for cost savings by deleting these resources when they are no longer needed. 

