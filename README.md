# Cloud VM install steps

## Build
```bash
sudo apt-get update
sudo apt -y install maven gradle git nano vim mc openjdk-8-jdk-headless curl wget gem ruby ruby-dev apt-transport-https ca-certificates curl gnupg-agent software-properties-common
sudo update-java-alternatives -s java-1.8.0-openjdk-amd64
```
## CI
```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install -y jenkins
sudo ufw allow 8080/tcp
sudo ufw allow 8088/tcp
```
Map the port on the Cloud VM to the public interface, enter the aut code from /var/lib/jenkins/secrets/initialAdminPassword

Install plugins: GitParameter, GitHub

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get install docker-ce docker-ce-cli containerd.io

sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo usermod -a -G docker jenkins
sudo usermod -a -G docker cloud

sudo gem install travis
curl https://cli-assets.heroku.com/install-ubuntu.sh | sh
```

## Virtualization

```bash
echo "deb http://download.virtualbox.org/virtualbox/debian bionic contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
sudo apt update
sudo apt-get -y install virtualbox-6.0
sudo usermod -aG vboxusers cloud

wget https://releases.hashicorp.com/vagrant/2.2.5/vagrant_2.2.5_x86_64.deb
sudo dpkg -i vagrant*.deb

wget https://releases.hashicorp.com/packer/1.4.4/packer_1.4.4_linux_amd64.zip
unzip packer_1.4.4_linux_amd64.zip
sudo mv packer /usr/local/bin

sudo usermod -aG vboxusers jenkins
sudo usermod -aG sudo jenkins
echo "jenkins   ALL=(ALL:ALL) NOPASSWD: ALL" | sudo tee -a /etc/sudoers

sudo apt install -y p7zip-full
wget -O precise32.7z https://sourceforge.net/projects/osboxes/files/v/vb/55-U-u/12.04/120432.7z/download
7z x precise32.7z
```

## Docker
```bash
sudo apt-get install -y snap snapd
sudo snap install microk8s --classic
sudo ufw allow in on cni0 && sudo ufw allow out on cni0
sudo ufw default allow routed
```

Add /snap/bin to the PATH

```bash
sudo usermod -a -G microk8s cloud

echo ". /snap/microk8s/current/kubectl.bash" >> /home/cloud/.bashrc
echo "alias kubectl=microk8s.kubectl" >> /home/cloud/.bashrc

microk8s.enable ingress dns
sudo ufw allow 443
```

## AWS
```bash
sudo apt -y install python3-pip mysql-client-core-5.7 
sudo -H pip3 install awscli --upgrade
```
Install Jenkins AWSEB Deployment plugin


