# CREATE USER AND ENABLE PASS AUTH AWS

## CREATE NEW USER ON EC2 LINUX INSTANCE

First, login into ( SSH ) your EC2 instance with default username which is ec2-user using your private-key file that has the extension (.pem)

```bash
# login to your instance
ssh -i "/path/to/pem/file" <user>@<public-ip-address>
```

Now we have sshed into the linux instance, create user

```bash
# create new user
sudo useradd -c "<username>" -m <username>

# set password
echo "<username>:<password>" | sudo chpasswd

# or

# alternatively
sudo useradd -m -p <password> -s /bin/bash <user>

# -m: --create-home
# -p: --password
# -s: --shell

# list users
cut -d: -f1 /etc/passwd

# grant him a sudo privilege by assign it to the root group
sudo usermod -aG sudo <user>

# switch to user
su - <user>
```

## CONFIGURE NEW USER SSH CONFIG

Configure ssh for the new user account

`Note: Without these exact file permissions, the user will not be able to log in.`

```bash
# make sure you are in the <user> directory I.E /home/<user>
# create a .ssh directory in the <user> home directory
mkdir .ssh

# set the correct permission at .shh directory 
chmod 700 .ssh

# create file inside .ssh directory to store the public key 
touch .ssh/authorized_keys

# set user read/write permission at authorized_keys file 
chmod 600 .ssh/authorized_keys

```

Now we have to retrieve the public key to use for sshing into the instance with this user. _`This particular process is done on the machine that has the .pem file you intend to use`_

```bash
ssh-keygen -y -f /path_to_key_pair/my-key-pair.pem

# sample output

ssh-rsa AAAA...AMPLE
```

In your remote instance copy and paste the output of the previous command into `.ssh/authorized_keys` file in `/home/<user>`

```bash
sudo vim .ssh/authorized_keys 

# exit with :wq!

# Ensure that you paste the public key in one continuous line. The public key must not be split over multiple lines.
```

## ENABLE SSH PASSWORD AUTHENTICATION

We need to enable sshing using password. 

```bash
# open sshd_config then set PasswordAuthentication to yes 
sudo vim /etc/ssh/sshd_config

# after saving run this command
sudo service sshd restart
```