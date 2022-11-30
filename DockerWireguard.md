## Docker Project with Wireguard

### DigitalOcean Account
The first step is to create an account in [DigitalOcean](https://m.do.co/c/4d7f4ff9cfe4) (this link will get you $200 credit for 2 months of usage). Create a new account regardless of whether you have an account or not in DigitalOcean with the link provided. You can create your account through Google, Github, or email (I chose email). After you sign up, they will ask for verfication through email. They will also ask for a payment method to verify your account. Make sure you delete your account once you are done using it to prevent unwanted payments. Click the top left logo or "Explore our control-panel" link to begin creating Ubuntu 20.04 Droplet.

![DigitalOcean Account](https://user-images.githubusercontent.com/113713588/204705801-a5c682d9-5bf3-48f1-ab67-8bee4206307e.png)

### Creating Ubuntu 20.04 Droplet
- On the left under "Manage", click "Droplets" and then click "Create Droplet". 
- **Choose an image** 
  - When selecting the image, to save a step, click the "Marketplace" tab and select Docker on Ubuntu 20.04. 
- **Choose a plan**
  - Select the "Basic" (shared CPU) plan.
  - Select "Regular with SSD" for CPU options
  - Select the cheapest monthly/hourly option. 
- **Choose a datacenter region**
  - Select the closest datacenter to you. 
- **Authentication**
  - Select an authentication method of your choice. Select SSH keys if you already have it set up since it is more secure. However, choosing the password method will be enough and it is easier to set up. 
  - Leave other customizations alone and click the "Create Droplet" button
  - Wait until Droplet is fully installed.

![Ubuntu 20 04 Droplet](https://user-images.githubusercontent.com/113713588/204712164-b5bf49f4-a698-462e-a826-ce610ca40497.png)

### Installing Docker
Docker was installed in the previous step when choosing an image. If you didn't install Docker in the previous step, follow the instructions in this [link](https://thematrix.dev/setup-wireguard-vpn-server-with-docker/).

### Installing Wireguard
