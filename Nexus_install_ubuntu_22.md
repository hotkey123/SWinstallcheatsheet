#### Installing Nexus as a service on UBUNTU 20.04 #####


### Requirements ###


- Use t2.medium server from AWS because it needs 4GB of RAM and 20GB of space

  - To check OS-version UBUNTU:
	- cat /etc/os-release

  - Updating server:
	- sudo apt-get update

  - Inastall JAVA:
	- sudo apt install openjdk-8-jre-headless
	- java -version

  - Move to /opt directory:
	- cd /opt/

  - Download Nexus repo:
	- sudo wget https://download.sonatype.com/nexus/3/nexus-3.40.1-01-unix.tar.gz
	- sudo tar -xvzf nexus-3.40.1-01-unix.tar.gz
	- sudo mv /opt/nexus-3.40.1-01 /opt/nexus
	- cd nexus/

  - Adding USER Nexus:
	- sudo adduser nexus
	- sudo visudo
		nexus ALL=(ALL) NOPASSWD: ALL 	- 	(Under Root Line)

  - Changing folder permission to Nexus:
	- sudo chown -R nexus:nexus /opt/nexus
	- sudo chown -R nexus:nexus /opt/sonatype-work/

  - Adding Nexus User in nexus.rc file:
	- sudo vi /opt/nexus/bin/nexus.rc
		- run_as_user="nexus"
	
  - Adding memory content in vmoptions file:
	- sudo vi /opt/nexus/bin/nexus.vmoptions
			-Xms1024m
			-Xmx1024m
			-XX:MaxDirectMemorySize=1024m

			-XX:LogFile=./sonatype-work/nexus3/log/jvm.log
			-XX:-OmitStackTraceInFastThrow
			-Djava.net.preferIPv4Stack=true
			-Dkaraf.home=.
			-Dkaraf.base=.
			-Dkaraf.etc=etc/karaf
			-Djava.util.logging.config.file=/etc/karaf/java.util.logging.properties
			-Dkaraf.data=./sonatype-work/nexus3
			-Dkaraf.log=./sonatype-work/nexus3/log
			-Djava.io.tmpdir=./sonatype-work/nexus3/tmp
	
  - Create a systemd service file to manage the Nexus service:
	- sudo vi /etc/systemd/system/nexus.service
			[Unit]
			Description=nexus service
			After=network.target

			[Service]
			Type=forking
			LimitNOFILE=65536
			ExecStart=/opt/nexus/bin/nexus start
			ExecStop=/opt/nexus/bin/nexus stop
			User=nexus
			Restart=on-abort

			[Install]
			WantedBy=multi-user.target
	
  - Starting Nexus service:
	- sudo systemctl start nexus

  - Checking status of Nexus service:
	- sudo systemctl status nexus

  - Enabling Nexus service over restart:
	- sudo systemctl enable nexus

  - Open the Nexus Repository Manager on Web browser: 
	- http://<Ip_address>:8081

  - Sign in as admin user and password will store in the below location:
	- sudo cat /opt/nexus/sonatype-work/nexus3/admin.password
