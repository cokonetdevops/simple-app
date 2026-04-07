# Simple App

### Step 1: Install Node.js (Version 22)
To install Node.js v22 on Ubuntu, you can use the NodeSource PPA:
```bash
# Update package index
sudo apt update

# Install curl if not already installed
sudo apt install curl -y

# Download and install the NodeSource setup script for Node.js 22
curl -fsSL https://deb.nodesource.com/setup_22.x -o nodesource_setup.sh
sudo -E bash nodesource_setup.sh

# Install Node.js
sudo apt install -y nodejs

# Verify the installation
node -v
npm -v
```

### Step 2: Transfer Your Application Code to the EC2 Instance
You need to get your code (the `package.json`, `src/`, and `public/` directories) onto the EC2 instance. You have a few options:

**Clone from Git**
If your code is in a repository (like GitHub or GitLab):
```bash
git clone <your-repository-url>
cd <your-repository-directory>
```

### Step 3: Install Dependencies and Build the application

```bash
# 1. Install local dependencies
npm install

# 2. Install the 'serve' package globally (requires sudo on Ubuntu)
sudo npm install -g serve@latest

# 3. Build the application
npm run build

# 4. Remove local node_modules to save space (Optional)
rm -fr node_modules
```

### Step 4: Configure AWS Security Group

1. Go to your AWS EC2 Console.
2. Select your instance and click on the **Security** tab.
3. Click the Security Group assigned to the instance.
4. Click **Edit inbound rules**.
5. Add a new rule: 
   - **Type**: Custom TCP
   - **Port range**: 3000
   - **Source**: Custom `0.0.0.0/0` (or `Anywhere-IPv4`) to allow anyone to view it.
6. Save rules.

### Step 5: Start the Application
Finally, start your static server:
```bash
serve -s build -l 3000
```

> [!TIP]
> Running the `serve` command as shown above will stop the app if you close your SSH session. To keep the application running in the background persistently, it's highly recommended to use **PM2** (a production process manager for Node.js):
> 
> ```bash
> # Install PM2 globally
> sudo npm install -g pm2
> 
> # Start the app using PM2
> pm2 start "serve -s build -l 3000" --name "my-webapp"
> 
> # Make it restart automatically on server reboots
> pm2 startup
> pm2 save
> ```

You should now be able to access your web application by visiting `http://<your-ec2-public-ip>:3000` in your browser.
