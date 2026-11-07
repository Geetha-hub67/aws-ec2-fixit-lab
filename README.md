# AWS EC2 Fix-it Lab

## What I built
I launched an Amazon Linux 2023 EC2 instance (t3.micro) named `fixit-web-server` and used a startup script to install an NGINX web server. I opened only HTTP (port 80) in the security group and kept SSH closed. I connected securely through Session Manager using an IAM role, so no port was exposed. To practice troubleshooting, I broke the setup on purpose in two different ways and fixed each one.

## Architecture
```
Browser (HTTP, port 80)
        |
Security group (allows port 80 only)
        |
EC2 instance (Amazon Linux 2023, t3.micro) running NGINX
        |
IAM role (ec2-ssm-role) -> Session Manager (browser terminal, no SSH)
```

## Problem 1: Firewall rule removed
- **Symptom:** The website did not open and the browser showed `ERR_CONNECTION_TIMED_OUT`.
- **Diagnosis:** The instance was running and passed all status checks, so the server was healthy. I used `http://` and not `https://`. I opened the security group's inbound rules and found the HTTP (port 80) rule was missing.
- **Fix:** I added an inbound rule: Type HTTP, Port 80, Source 0.0.0.0/0.
- **Result:** The website loaded again.

## Problem 2: Web service stopped
- **Symptom:** The website did not open and the browser showed `ERR_CONNECTION_REFUSED`.
- **Diagnosis:** The security group allowed port 80, so traffic was reaching the server. I connected to the instance through Session Manager and ran `systemctl status nginx`, which showed the service was stopped.
- **Fix:** I ran `sudo systemctl start nginx` and checked that the status showed `active (running)`.
- **Result:** The website loaded again.

## What I learned
- **Timeout** means the request never reached the server, so I check the network path (security group, protocol, IP address).
- **Connection refused** means the server answered but nothing was listening, so I check the service (`systemctl status`).
- **404** means the server answered but the page was missing.
- Session Manager lets me manage a server without opening SSH to the internet.
- Giving a role only the permission it needs (least privilege) is safer than using full admin access.

## Clean-up
I terminated the instance after each session so no resources kept running and no credits were wasted.
