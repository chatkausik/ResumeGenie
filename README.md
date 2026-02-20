# ResumeGenie

![Logo](logo.png)

# 🚀 Resume Genie

> **AI-powered resume toolkit built on Grok-4 (xAI) — craft winning resumes, cover letters & career strategies in seconds.**

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-1.35+-FF4B4B?style=flat-square&logo=streamlit&logoColor=white)
![Grok-4](https://img.shields.io/badge/Powered%20by-Grok--4%20xAI-000000?style=flat-square&logo=x&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-0.2+-1C3C3C?style=flat-square&logo=chainlink&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)

---

## ✨ Features

| Tool | Description |
|------|-------------|
| ✉️ **Cover Letter Generator** | Generates a tailored 300–450 word cover letter by matching your resume to a job description |
| 📊 **Resume-JD Matcher** | Scores your resume against a job description with keyword analysis, ATS compatibility & skill gap insights |
| 🔍 **Resume Checker** | Standalone resume evaluator — rates clarity, format, ATS-readiness & recommends next career steps |
| 💬 **Career Coach Chat** | Conversational AI coach powered by your resume context for interview prep, career advice & more |

---

## 🖥️ Demo Screenshot

```
┌─────────────────────────────────────────────────────────────┐
│  📄 Resume Genie                              Powered by xAI │
├──────────────┬──────────────────────────────────────────────┤
│  🛠 Tools    │   ✉️ AI Cover Letter Generator                │
│              │                                              │
│ ✉️ Cover     │  📝 Job Description   │  📄 Your Resume      │
│ 📊 Matcher   │  ─────────────────   │  ─────────────────   │
│ 🔍 Checker   │  Paste JD here...    │  Upload PDF ↑        │
│ 💬 Coach     │                      │                      │
│              │                      │  🔥 Generate Letter  │
└──────────────┴──────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```
resume-genie/
├── main.py                  # Core Streamlit application
├── logo.png                 # Sidebar logo
├── requirements.txt         # Python dependencies
├── .env                     # Local environment variables (not committed)
├── .streamlit/
│   └── secrets.toml         # Streamlit Cloud secrets
└── README.md
```

---

## ⚙️ Local Setup

### 1. Clone the repository

```bash
git clone https://github.com/chatkausik/ResumeGenie.git
cd resume-genie
```

### 2. Create a virtual environment

```bash
python -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

> **requirements.txt** should include:
> ```
> streamlit
> langchain
> langchain-xai
> langchain-community
> pypdf
> pillow
> python-dotenv
> ```

### 4. Set up your xAI API key

Create a `.env` file in the project root:

```env
XAI_API_KEY=your_xai_api_key_here
```

Or create `.streamlit/secrets.toml`:

```toml
XAI_API_KEY = "your_xai_api_key_here"
```

### 5. Run the app

```bash
streamlit run main.py
```

Open [http://localhost:8501](http://localhost:8501) in your browser. 🎉

---

## 🌐 Deployment Guide — AWS

> Deploy Resume Genie on **AWS EC2** for a production-ready, always-on experience.

### Prerequisites
- AWS account with EC2 access
- Key pair (`.pem` file) for SSH
- Domain name (optional, for HTTPS)

---

### Step 1 — Launch an EC2 Instance

1. Go to **AWS Console → EC2 → Launch Instance**
2. Choose settings:
   - **AMI**: Ubuntu Server 22.04 LTS (Free Tier eligible)
   - **Instance type**: `t3.small` (recommended) or `t2.micro` (free tier)
   - **Key pair**: Create or select an existing `.pem` key
3. Under **Security Group**, add inbound rules:

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| SSH | TCP | 22 | Your IP |
| Custom TCP | TCP | 8501 | 0.0.0.0/0 |
| HTTP | TCP | 80 | 0.0.0.0/0 *(optional)* |
| HTTPS | TCP | 443 | 0.0.0.0/0 *(optional)* |

4. Launch the instance and note the **Public IPv4 address**.

---

### Step 2 — Connect & Configure the Server

```bash
# Connect via SSH
ssh -i your-key.pem ubuntu@<your-ec2-public-ip>

# Update system packages
sudo apt update && sudo apt upgrade -y

# Install Python & pip
sudo apt install python3-pip python3-venv git -y
```

---

### Step 3 — Deploy the Application

```bash
# Clone your repo
git clone https://github.com/chatkausik/ResumeGenie.git
cd resume-genie

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

---

### Step 4 — Configure Environment Variables

```bash
# Option A: .env file
echo "XAI_API_KEY=your_xai_api_key_here" > .env

# Option B: Streamlit secrets
mkdir -p .streamlit
cat > .streamlit/secrets.toml << EOF
XAI_API_KEY = "your_xai_api_key_here"
EOF
```

---

### Step 5 — Run as a Background Service (systemd)

Create a systemd service so the app restarts automatically on reboot:

```bash
sudo nano /etc/systemd/system/resume-genie.service
```

Paste the following (adjust paths as needed):

```ini
[Unit]
Description=Resume Genie Streamlit App
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/resume-genie
Environment="PATH=/home/ubuntu/resume-genie/venv/bin"
ExecStart=/home/ubuntu/resume-genie/venv/bin/streamlit run main.py \
    --server.port 8501 \
    --server.address 0.0.0.0 \
    --server.headless true
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable resume-genie
sudo systemctl start resume-genie

# Check status
sudo systemctl status resume-genie
```

Your app is now live at: **`http://<your-ec2-public-ip>:8501`** 🎉

---

### Step 6 (Optional) — Custom Domain + HTTPS with Nginx

Install Nginx as a reverse proxy:

```bash
sudo apt install nginx -y
```

Create a config file:

```bash
sudo nano /etc/nginx/sites-available/resume-genie
```

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8501;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Enable the config:

```bash
sudo ln -s /etc/nginx/sites-available/resume-genie /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

Add HTTPS with Let's Encrypt:

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

Your app is now secured at **`https://yourdomain.com`** 🔒

---

### Step 7 — Useful Management Commands

```bash
# View live logs
sudo journalctl -u resume-genie -f

# Restart the app (after code changes)
sudo systemctl restart resume-genie

# Pull latest code & restart
cd /home/ubuntu/resume-genie && git pull && sudo systemctl restart resume-genie
```

---

## 🔐 Security Best Practices

- Never commit `.env` or `secrets.toml` to version control — add them to `.gitignore`
- Restrict SSH access to your IP only in the EC2 security group
- Rotate your `XAI_API_KEY` periodically
- Use IAM roles instead of hardcoded credentials for any AWS service integration
- Enable **AWS CloudWatch** for monitoring and alerting

---

## 🤝 Contributing

Pull requests are welcome! For major changes, please open an issue first.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License**.

---

## 👨‍💻 Author

Built with ❤️ by **Kausik Chattapadhyay** • January 2026

[![xAI](https://img.shields.io/badge/Powered%20by-xAI%20Grok--4-000?style=flat-square&logo=x)](https://x.ai)
[![Streamlit](https://img.shields.io/badge/Built%20with-Streamlit-FF4B4B?style=flat-square&logo=streamlit)](https://streamlit.io)