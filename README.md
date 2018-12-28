# Linux Server Configuration Project

This is the last project for us as a students of Udacity Full Stack Web Developer Nanodegree program.
 
## Introduction

This project is a good tutorial on how to build a secure server, install and configure a database server, and deploy a web application into it.

## Project Overview

A baseline installation of a Linux server [Amazon Lightsail](https://signin.aws.amazon.com) and prepare it to host [Item Catalog](https://github.com/kalthommusa/Item_Catalog_Project2) web applications.

## IP 

  * IP Address: http://52.47.127.157.xip.io

## Amazon Lightsail

  1. First, log in to [Amazon Lightsail](https://signin.aws.amazon.com). If you don't already have an Amazon Web Services account, you'll be prompted to create one.

  2. Create an instance. Once you're logged in, Lightsail will give you a friendly message with a robot on it, prompting you to create an instance. A Lightsail instance is a Linux server running on a virtual machine inside an Amazon datacenter.

  3. Choose an instance image: Ubuntu Lightsail supports a lot of different instance types. An instance image is a particular software setup, including an operating system and optionally built-in applications. There are two settings to make here. First, choose "OS Only" (rather than "Apps + OS"). Second, choose Ubuntu as the operating system.

  4. Choose your instance plan. The instance plan controls how powerful of a server you get. It also controls how much money they want to charge you. For this project, the lowest tier of instance is just fine

  5. Give your instance a hostname. Every instance needs a unique hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it. 

  6. It's running; let's use it! Once your instance has started up, you can log into it with SSH from your browser. 

The public IP address of the instance is displayed along with its name.

My public IP address of the instance is : 52.47.127.157

## Instance Configuration and Connection 

  1. Download your instance Private Key from your profile, which starts with "LightsailDefaultKey" and with ".pem" extention.

  2. In your local machine, rename the file to "Lightsail_Key.rsa " and save it in `~/.ssh/`

  3. Change the permission: `chmod 600 ~/.ssh/Lightsail_key.rsa`

  4. Connect to our Amazon Lightsail instance by using Ubuntu User `ssh -i ~/.ssh/Lightsail_Key.rsa -p 22 ubuntu@[PUT YOUR PUBLIC IP ADDRESS, IN MY CASE IS 52.47.127.157]`
  Congrats!, you officially now have access to the server.

## Secure the Server

  1. Update all currently installed packages, run the following commands :
     * `sudo apt-get update`
     * `sudo apt-get upgrade`

  2. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
     * Open ssh config , run the following command :
     `sudo nano /etc/ssh/sshd_config`

     * Change the "Port 22" in the file to "Port 2200"

     * Restart ssh service, run the following command :
     `sudo service ssh restart`


  3. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123), run the following commands :
     * `sudo ufw allow 2200`
     * `sudo ufw allow 80`
     * `sudo ufw allow 123`

     Then enable ufw, run the following command :
     * `sudo ufw enable`
