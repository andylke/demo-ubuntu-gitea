# Setup Gitea

## Install Git

`sudo apt install git`

## Install Gitea running on MySQL

Create gitea database and user:

```
mysql -u root -padmin@123
CREATE DATABASE gitea;
CREATE USER 'gitea'@'localhost' IDENTIFIED BY 'gitea@123';
GRANT ALL PRIVILEGES ON gitea.* TO 'gitea'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit
```

Create a Git user to run Gitea services:
`sudo adduser --system --group --disabled-password --shell /bin/bash --home /home/git --gecos 'Git' git`

Download:
`wget -O /tmp/gitea https://dl.gitea.io/gitea/1.17.0/gitea-1.17.0-linux-amd64`

Check `https://dl.gitea.io/gitea` for the latest version of gitea.

Gitea binary file to `/usr/local/bin` and make the binary executable file:

```
sudo mv /tmp/gitea /usr/local/bin
sudo chmod +x /usr/local/bin/gitea
```

Next, we create the directory structure and set the required permissions and ownership:

```
sudo mkdir -p /var/lib/gitea/{custom,data,indexers,public,log}
sudo chown git: /var/lib/gitea/{data,indexers,log}
sudo chmod 750 /var/lib/gitea/{data,indexers,log}
sudo mkdir /etc/gitea
sudo chown root:git /etc/gitea
sudo chmod 770 /etc/gitea
```

Download the gitea.service file to the `/etc/systemd/system/`:

`sudo wget https://raw.githubusercontent.com/go-gitea/gitea/main/contrib/systemd/gitea.service -P /etc/systemd/system/`

Reload systemd and start Gitea service with the following command:

```
sudo systemctl daemon-reload
sudo systemctl enable --now gitea
sudo systemctl status gitea
```

Open Gitea at `http://192.168.1.10:3000/`

Setup Gitea admin
username: admin
password: admin@123

## Configure SSL on Gitea

Copy to `/etc/gitea`

```
sudo cp server.crt /etc/gitea
sudo cp server.key /etc/gitea
sudo chown -R git: /etc/gitea
sudo chmod 700 /etc/gitea
sudo chmod 644 /etc/gitea/server.crt
sudo chmod 644 /etc/gitea/server.key
```

`sudo nano /etc/gitea/app.ini`

```
[server]
HTTP_PORT        = 5000
ROOT_URL         = https://192.168.1.10:3000/
PROTOCOL         = https
CERT_FILE        = /etc/gitea/server.crt
KEY_FILE         = /etc/gitea/server.key
```
