---
title: Configuring bare metal Vultr servers with cloud-init
subtitle: Let's look at how cloud-init can be used to provision infrastructure
date: '2020-04-09'
thumb_img_path: >-
  https://images.ctfassets.net/ocdgldwpic0i/pltnoD2eUwFo7d0RkV4u9/605e180b8f4dd66f63e303f8f33b0bd5/c6f93a678e8c776940f5aa937a4bffaf780bad9c-1460x868.png
thumb_img_alt: vultr-cloud-init
content_img_path: >-
  https://images.ctfassets.net/ocdgldwpic0i/pltnoD2eUwFo7d0RkV4u9/605e180b8f4dd66f63e303f8f33b0bd5/c6f93a678e8c776940f5aa937a4bffaf780bad9c-1460x868.png
content_img_alt: vultr-cloud-init
excerpt: >-
  Oftentimes there will be cases where you will want to automate the
  provisioning and configuration of your Vultr cloud infrastructure. There are a
  plethora of tools out there, however, cloud-init is an industry-standard that
  is used to initialize and configure VM instances with user-data.
seo:
  title: Configuring bare metal Vultr servers with cloud-init
  description: >-
    Apparently, Japan is covered in magical and irresistibly cute animal
    sanctuaries.
  extra:
    - name: 'og:type'
      value: article
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:title'
      value: Fox Village In Japan
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:description'
      value: >-
        Apparently, Japan is covered in magical and irresistibly cute animal
        sanctuaries.
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:image'
      value: images/5.jpg
      keyName: property
      relativeUrl: true
      type: stackbit_page_meta_extra
    - name: 'twitter:card'
      value: summary_large_image
      type: stackbit_page_meta_extra
    - name: 'twitter:title'
      value: Fox Village In Japan
      type: stackbit_page_meta_extra
    - name: 'twitter:description'
      value: >-
        Apparently, Japan is covered in magical and irresistibly cute animal
        sanctuaries.
      type: stackbit_page_meta_extra
    - name: 'twitter:image'
      value: images/5.jpg
      relativeUrl: true
      type: stackbit_page_meta_extra
  type: stackbit_page_meta
template: post
stackbit_url_path: /posts/configuring-bare-metal-vultr-servers-with-cloud-init
---
## Introduction

Oftentimes there will be cases where you will want to automate the provisioning and configuration of your Vultr cloud infrastructure. There are a plethora of tools out there, however, cloud-init is an industry-standard that is used to initialize and configure VM instances with user-data.

### What is Terraform?

Terraform is an Infrastructure-as-code tool that allows users to build, change, and version your infrastructure safely and efficiently. It uses a high-level syntax to declaratively provision and manage infrastructure, allowing the ability to break down the configuration into smaller chunks for better organization, re-use, and maintainability. Information on installing and running Terraform can be found [here](https://www.terraform.io/intro/index.html). By passing the user_data parameter into a Terraform.yaml file, you can use automation to configure your Vultr instance at boot time. More on that below.

### Using Terraform to configure Cherryservers with cloud-init

If Terraform is your preferred infrastructure provisioning method then you can find the Vultr Terraform Provider at the Github Repo [here](http://downloads.cherryservers.com/other/terraform/).

For any infrastructure provider, when using Terraform as a provisioning tool you will always need to specify the provider block as seen here:

Here's an example module that utilizes user-data to configure a Vultr instance at boot time:

With this module, you have a resource that is designating vultr_server as the type of resource you want to provision, and using variables such as project_id and user_data to handle the provisioning. When you provide the string for user_data, you are designating a startup script that the bare-metal server will run on boot-up.

### Using cloud-init to configure Vultr servers

You can provision new servers via the API to fetch user data of your Vultr via the cloud-init service. This allows you to automate various server configuration tasks by fetching user data directives upon server deployment. Your provided tasks will be executed when your server boots for the first time. There are two ways of doing this - **shell scripts** or **cloud-init** directives. We're going to talk about cloud-init directives.

Cloud-Init directives are executed when your server boots for the first time, but the syntax is slightly different. Your scenario must start with #cloud-config line, otherwise user data directives will be rejected. For further reference, I recommend checking the cloud-init official documentation: <https://cloudinit.readthedocs.io/en/latest/index.html>

*A simple example of a cloud-init script that would be passed is:*

In order to pass this data scenarios to Vultr API, it must be converted into **base64** format. On a Linux system you would do the following for your test.yaml file:

This output text then has to be fetched via Vultr API **user_data** parameter when ordering a new server.

### Putting it all together

To see this in action, specify the resources provider so that you can designate Vultr as the platform you're provisioning to. Your final script should look like this:

## Finishing up

That's all there is to it! Terraform is a really great tool for automating infrastructure once you understand the syntax and how it works. I hope you liked this article!
