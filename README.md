# AI macOS Agent

A comprehensive toolkit for running AI services locally on macOS in a containerized environment, combining the power of n8n workflow automation and Agent Zero AI assistants.

## *"Good artists copy, great artists steal."*  
(PabloPicasso)  

![Feininger](https://github.com/netzbub/AI-macOS-Agent/blob/main/docs/images/feininger.jpg)  
Lyonel Feininger

### Table of Contents
- [AI macOS Agent](#ai-macos-agent)
  - [*"Good artists copy, great artists steal."*](#good-artists-copy-great-artists-steal)
    - [Table of Contents](#table-of-contents)
    - [Inspiration](#inspiration)
    - [Overview](#overview)
    - [Features](#features)
    - [Prerequisites](#prerequisites)
    - [Installation](#installation)
    - [Configuration](#configuration)
    - [Directory Structure](#directory-structure)
    - [Usage](#usage)
    - [Accessing Services](#accessing-services)
    - [Working with Local Files](#working-with-local-files)
    - [Backup to Synology NAS](#backup-to-synology-nas)
    - [Maintenance](#maintenance)
    - [Updating Services](#updating-services)
    - [Checking Logs](#checking-logs)
    - [License](#license)
    - [Acknowledgments](#acknowledgments)


### Inspiration

The following repositories inspired me to build my own local **AI macOS Agent** - up to now a first sketch on the wall and it will have to be improved over time.

- [Original Local AI Starter Kit](https://github.com/n8n-io/self-hosted-ai-starter-kit) by the n8n team
- [Original self-hosted-ai-starter-kit-3](https://github.com/tuyenhm68/self-hosted-ai-starter-kit-3) by tuyenhm68
- [Original agent-zero](https://github.com/frdel/agent-zero) by Jan Tomášek and his contributors

### Overview

This project integrates two powerful AI frameworks:
- **AI macOS Agent**: Provides n8n workflow automation with 400+ integrations and AI components
- **Agent Zero**: Offers AI agents with terminal and web interfaces

All services are accessible through a central Astroluma dashboard and secured with local SSL certificates.

### Features

- **Central Dashboard**: Astroluma for managing all services
- **Workflow Automation**: n8n with 400+ integrations and AI components
- **AI Agents**: Agent Zero for terminal and web-based AI assistance
- **Local LLM Integration**: Direct connection to locally installed Ollama via host.docker.internal bridge, allowing containers to access Ollama running on the host system
- **SSL Security**: Local domains secured with mkcert certificates
- **Reverse Proxy**: Traefik for routing and SSL termination
- **Backup Solution**: Optimized for Synology NAS

### Prerequisites

- macOS (tested on macOS 15.5)
- [Homebrew](https://brew.sh/)
- [Docker/Orbstack](https://orbstack.dev/)
- [Ollama](https://ollama.ai/) - installed locally, not locally in a container
- [mkcert](https://github.com/FiloSottile/mkcert) - it has to be installed on your machine before running the installer, the bash-script will setup the SSL Certificates for the local domain and subdomain which you setup in the .env file.

**Be very (!) careful** with choosing a TLD for your local network. The most recommended two ways, that will not conflict with existing local or public domains and with public DNS, are either to use [.home.arpa](https://home.arpa). The second recommended way is to use a subdomain of a domain, that you 'own' like [sub.mydomain.com](https://sub.mydomain.com). Be aware, that mkcert only supports sub.mydomain.com but will not work with next.sub.mydomain.com.

Add to /etc/hosts:
```
127.0.0.1   mac.home.arpa
127.0.0.1   luma.home.arpa
127.0.0.1   n8n.home.arpa
127.0.0.1   agent.home.arpa
127.0.0.1   traefik.home.arpa
```

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/netzbub/ai-macos-agent.git
   cd ai-macos-agent
   ```

2. Copy the example environment file and edit it:
   ```bash
   cp .env.example .env
   # Edit .env with your preferred settings
   ```

3. Run the setup script:
   ```bash
   chmod +x scripts/setup.sh
   ./scripts/setup.sh
   ```

4. Access the services:
   - Dashboard: https://luma.home.arpa
   - n8n: https://n8n.home.arpa
   - Agent Zero: https://agent.home.arpa
   - Traefik: https://traefik.home.arpa

### Configuration

The central `.env` file contains all configuration options:

```
# General Settings
DOMAIN=home.arpa

# Port Settings
N8N_PORT=5678
ASTROLUMA_PORT=8000
AGENT_ZERO_PORT=80
TRAEFIK_DASHBOARD_PORT=8080

# n8n Settings
N8N_ENCRYPTION_KEY=your-encryption-key
POSTGRES_PASSWORD=your-postgres-password

# Ollama Integration
OLLAMA_HOST=host.docker.internal
OLLAMA_PORT=11434
```

### Directory Structure

```
.
├── .env.example
├── .git
├── .gitignore
├── .history
├── config
│   └── traefik
│       ├── dynamic
│       │   └── tls.yml
│       └── traefik.yml
│       └── certs/
├── data/
│   ├── agent-zero/
│   ├── astroluma/
│   ├── n8n/
│   └── postgres/
├── docker-compose
│   ├── agent-zero.yml
│   ├── astroluma.yml
│   ├── n8n.yml
│   └── traefik.yml
├── docs
│   ├── CODE_OF_CONDUCT.md
│   ├── CONTRIBUTING.md
│   └── images
│       └── feininger.jpg
├── README.md
├── scripts
│   ├── backup-synology.sh
│   ├── generate-certs.sh
│   ├── setup-n8n.sh
│   ├── setup-volumes.sh
│   ├── setup.sh
│   └── validate-services.sh
└── shared
```

### Usage

### Accessing Services

All services are accessible through your browser using the following URLs:
- **Astroluma Dashboard**: https://luma.home.arpa
- **n8n Workflow Automation**: https://n8n.home.arpa
- **Agent Zero**: https://agent.home.arpa
- **Traefik Dashboard**: https://traefik.home.arpa

### Working with Local Files

Place files you want to access from the services in the `shared` directory:
- Files will be available in n8n at `/data/shared`
- Files will be available in Agent Zero at `/a0/shared`

### Backup to Synology NAS

Configure the backup script with your Synology details:
```bash
nano scripts/backup-synology.sh
# Update NAS_USER, NAS_IP, and other parameters
```

Run the backup manually or set up a cron job:
```bash
chmod +x scripts/backup-synology.sh
./scripts/backup-synology.sh
```

### Maintenance

### Updating Services

```bash
cd docker-compose
docker-compose -f traefik.yml -f astroluma.yml -f n8n.yml -f agent-zero.yml pull
docker-compose -f traefik.yml -f astroluma.yml -f n8n.yml -f agent-zero.yml up -d
```

### Checking Logs

```bash
docker logs traefik
docker logs astroluma
docker logs n8n
docker logs agent-zero
```

### License

This project combines components with different licenses:
- n8n starter kit: [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0)
- Agent Zero: [MIT License](https://opensource.org/licenses/MIT)

The integration code in this repository is licensed under the MIT License.

### Acknowledgments

- [n8n Self-hosted AI Starter Kit 3](https://github.com/tuyenhm68/self-hosted-ai-starter-kit-3)
- [Agent Zero](https://github.com/frdel/agent-zero)
- [Astroluma](https://github.com/astroluma/astroluma)
- [Traefik](https://traefik.io/)
- [Ollama](https://ollama.ai/)
