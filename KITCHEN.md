
# Install Kitchen

```bash
# Update the apt cache
sudo apt update

# Install required packages
sudo sudo apt-get install apt-transport-https ca-certificates curl software-properties-common build-essential ruby-dev ansible

# Get the chef DK ( including kitchen )
sudo wget https://packages.chef.io/files/stable/chefdk/3.2.30/ubuntu/16.04/chefdk_3.2.30-1_amd64.deb

# Install chef DK
sudo dpkg -i chefdk_3.2.30-1_amd64.deb

# Add docker repository to the system
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Install required kitchen ruby gems
sudo apt install docker-ce
sudo gem install kitchen-ansible
sudo gem install kitchen-docker
sudo gem install docker
sudo gem install kitchen-inspec
sudo gem install test-kitchen

# Go to your home folder
cd ~

# Get the source code from the repository
git clone ssh://danielpana@midas:29418/liveu
git clone ssh://danielpana@midas:29418/automation

# Fix the missing paths
ln -s ~/liveu/py ~/automation/liveu/py
ln -s ~/liveu/tools ~/automation/liveu/tools
ln -s ~/liveu/build ~/automation/liveu/build

# Go to the folder containing .kitchen.yml
cd ~/automation/auto/infra/sdk/deployment/ansiblefiles/

# Run kitchen test
kitchen test

```


