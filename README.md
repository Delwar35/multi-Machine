# multi-Machine

- New floder created
- Vagrantfile created
- Provision file created
- Created 2 VMs (app and db)
- lunched both VMs

**Creating Vagrantfile**
```
touch Vagrantfile
nano Vagrantfile
```
Creating multiple VMs (inside Vargrantfile)
```
Vagrant.configure("2") do |config|
  config.vm.provision "shell", path:"provision.sh"

#creating app vm
  config.vm.define "app" do |app|
    app.vm.box = "ubuntu/xenial64"
    app.vm.network "private_network", ip: "192.168.10.100"
    app.vm.synced_folder ".", "/home/vagrant/app"
  end

#creating db vm
  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/xenial64"
    db.vm.network "private_network", ip: "192.168.10.150"
    db.vm.synced_folder ".", "/home/vagrant/app"

  end
end

```
`db` and `app` where the two VMs created


**Creating Provision file**
```
touch Provision.sh
nano Provision.sh
```

Inside Provision file:
```
#!/bin/bash

#update
sudo apt-get update -y

#upgrade
sudo apt-get upgrade -y

#install nginx
sudo apt-get install nginx -y

#install  git
sudo apt-get install git -y

#install nodejs v6
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install nodejs -y

#install pm2
sudo npm install pm2 -g

# 192.168.10.100:3000 to access on browser

```

**NOTE: provision file must start with `#!/bin/bash` for it to be reconised as a provision file.**

**Accessing VMs**
`Vagrant up` will start the VMs
`Vagrant ssh db` will open the db VM
`Vagrant ssh app` will open the app VM

**Setting Up db mv**
- `Vagrant ssh db` to  open the db VM

- Install mongodb:
```
# be careful of these keys, they will go out of date
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927
echo "deb https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

sudo apt-get update -y
sudo apt-get upgrade -y

# sudo apt-get install mongodb-org=3.2.20 -y
sudo apt-get install -y mongodb-org=3.2.20 mongodb-org-server=3.2.20 mongodb-org-shell=3.2.20 mongodb-org-mongos=3.2.20 mongodb-org-tools=3.2.20
```
-check mongodb staus:
```
sudo systemctl restart mongodb
sudo systemctl enable mongodb
sudo systemctl status mongodb

```
- Config mondodb:
`sudo nano /etc/mongod.conf` will open the mongodb config file
Once in the config file change IP to 0.0.0.0 so everyone can access the file.

- Restart Mongodb
```
sudo systemctl restart mongodb
sudo systemctl enable mongodb
sudo systemctl status mongodb

```
`exit` to leave db VM

**Setting Up app mv**

#Connect app VM to db VM

`export DB_HOST="mongodb://192.168.10.150:27017/posts"` 192.168.10.150 is the IP of the db VM and the db is on port 27017
Refresh the db on the app VM by running `node seed.js` in the seeds dir (app/app/seeds)
`printenv DB_HOST` to check if the enviroment variable `DB_HOST`was created 
cd back to app/app and run the app 
`npm install`
`npm start`

**Setting up Reverse Proxy Server**
`sudo nano /etc/nginx/sites-available/default`

Within the server block you should have an existing location / block.
Replace the contents of that block with the following configuration.
If your application is set to listen on a different port, update `localhost:8080` to `localhost:correct port number (your port number)`.

```
    location / {
        proxy_pass http://localhost:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
 



