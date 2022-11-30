## Docker Project with Wireguard

### DigitalOcean Account
The first step is to create an account in [DigitalOcean](https://m.do.co/c/4d7f4ff9cfe4) (this link will get you $200 credit for 2 months of usage). Create a new account regardless of whether you have an account or not in DigitalOcean with the link provided. You can create your account through Google, Github, or email (I chose email). After you sign up, they will ask for verfication through email. They will also ask for a payment method to verify your account. Make sure you delete your account or destory the droplet once you are done using it to prevent unwanted payments. Click the top left logo or "Explore our control-panel" link to begin creating Ubuntu 20.04 Droplet.

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
I will be following this [guide](https://thematrix.dev/setup-wireguard-vpn-server-with-docker/) from theMatrixDev to install Wireguard. SSH into your droplet either using your terminal or droplet console in the "Access" tab from the website. I used the droplet console since it is easier.  

Within your console run these commands:
- ```mkdir -p ~/wireguard/```
- ```mkdir -p ~/wireguard/config/```
- ```nano ~/wireguard/docker-compose.yml```

Within the docker-compose.yml file you created, copy and paste the following content:  
```
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Hong_Kong
      - SERVERURL=1.2.3.4
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```

In your docker-compose.yml file, you will need to modify some of the content.
- Set "TZ" to the appropriate timezone of your location. Use this [link](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) to help you find your time zone.  
  For example, I set my TZ to	"America/Chicago". 
- Set SERVERURL to your Public IPv4 Address on your droplet. Can be found under the "Networking" tab. 

Start Wireguard by running these commands:
- ```sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose```
- ```sudo chmod +x /usr/local/bin/docker-compose```
- ```cd ~/wireguard/```
- ```sudo docker-compose up -d```
The server should be build after you see "Creating wireguard   ... done"

### Testing VPN on a mobile device
Start off by going to this website [IPLeak.net](IPLeak.net) on your mobile device and record your IP address.

![image](https://user-images.githubusercontent.com/113713588/204741061-ad98d153-5e6f-43ee-8cbf-557f3e2d1b03.png)

Install Wireguard application on your mobile device.  
Then in your console, type in this command ```docker-compose logs -f wireguard``` to build a QR code for a mobile device.

In the Wireguard application:
- Click the "+" sign and select "Scan From QR Code"
- Scan the QR Code that is labeled "PEER phone1 QR code".
- Give your tunnel a name (e.g. "DigitalOceanVPN") and click "Craete Tunnel".
- Toggle on your VPN.

![image](https://user-images.githubusercontent.com/113713588/204741270-1daa3c9e-5b5c-461a-96f8-39cbf6ab1ed0.png)

Go back to the [IPLeak.net](IPLeak.net) website and refresh to get your new ip address. The new IP address should be the same as the Public IPv4 address in droplet.

![image](https://user-images.githubusercontent.com/113713588/204741339-f8e79a15-5d5e-47fb-a4d1-fa689e5c0a9a.png)

### Testing VPN on a laptop
Check your IP Address using the [IPLeak.net](IPLeak.net) website. 

![image](https://user-images.githubusercontent.com/113713588/204742069-eb94ec97-3dd6-435e-85f9-a33e219daf35.png)

Within your console run these commands to access the .conf file: 
- ```cd ~/wireguard/config/peer_pc1```
- ```sudo nano peer_pc1.conf```

Copy the content of the .conf file and paste it into a text editor and save it as a .conf file.  
Install and open Wireguard on your laptop. 

Within the wireguard application on your laptop:
- Click "Import Tunnel(s) from File" and select the .conf file we created. 
- Click "Activate"

![image](https://user-images.githubusercontent.com/113713588/204750938-9514f8b9-4cbd-4777-8566-8e46642e376a.png)


Go back to the [IPLeak.net](IPLeak.net) website and refresh to get your new ip address. The new IP address should be the same as the Public IPv4 address in droplet.

![image](https://user-images.githubusercontent.com/113713588/204751072-9f25c728-8fba-45c2-af1c-8a2d12d0357a.png)
