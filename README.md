
In this project we will  prepare storage infrastructure on 2 Linux servers and implement a basic web solution using
WordPress. 
WordPress is a free and open-source content management system written in PHP and paired with MySQL as it's backend Relational Database Management System (RDBMS).

# Project consists of two parts:

1. Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical 
experience of working with disks, partitions and volumes in Linux.

2. Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying
 Web and DB tiers of Web solution.

# Three-tier Architecture

Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.

![3-tier Architecture ](/git/screenshots/3-tier%20Architecture.svg)
1. Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
2. Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
3. Data Access or Management Layer (DAL): This is the layer for computer data storage and data access. [Database](https://www.computerhope.com/jargon/d/database-server.htm) Server or File System Server such as [FTP](https://titanftp.com/2022/07/05/what-is-an-ftp-server/) server, or [NFS](https://www.techtarget.com/searchenterprisedesktop/definition/Network-File-System) Server

# Your 3-Tier Setup
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

we use RedHat OS for this project

# LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”.

1- Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB and Attach all three volumes.