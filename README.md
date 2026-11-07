# aws-ec2-fixit-lab
EC2 web server troubleshooting lab: security group and service failures

# AWS EC2 Fix-it Lab

## What I built
I launched an Amazon Linux 2023 EC2 instance (t3.micro) named `fixit-web-server` and used a startup script to install an NGINX web server. I opened only HTTP (port 80) in the security group and kept SSH closed. I connected securely through Session Manager using an IAM role, so no port was exposed. To practice troubleshooting, I stopped NGINX on purpose, saw the browser return "connection refused", then restarted the service and confirmed the site loaded again.

- **Symptom:** The website did not open and the browser showed `ERR_CONNECTION_TIMED_OUT`.
- **Diagnosis:** The instance was running and passed all status checks, so the server was healthy. I used `http://` and not `https://`. I opened the security group's inbound rules and found the HTTP (port 80) rule was missing.
- **Fix:** I added an inbound rule: Type HTTP, Port 80, Source 0.0.0.0/0.
- **Result:** The website loaded again.

## Clean-up
I terminated the instance after each session so no resources kept running.
