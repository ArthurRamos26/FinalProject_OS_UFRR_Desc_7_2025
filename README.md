# FinalProject_OS_UFRR_Desc_7_2025

Repositório destinado ao trabalho final da matéria Sistemas Operacionais na Universidade Federal De Roraima no ano de 2025

# Projeto de Virtualização e Containers

## Objetivo
Criar uma rede de computadores virtuais que se comuniquem entre si utilizando Máquinas Virtuais (ex: VirtualBox) e Containers (ex: Docker).

---

## 1. Instalação dos Softwares

### 1.1 VirtualBox
```bash
# Ubuntu/Debian
sudo apt update
sudo apt install virtualbox

# Fedora
sudo dnf install VirtualBox

# Windows
# Baixe em: https://www.virtualbox.org/wiki/Downloads
```

### 1.2 Docker
```bash
# Ubuntu
sudo apt update
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker

# Fedora
sudo dnf install docker
sudo systemctl start docker
sudo systemctl enable docker

# Verificação
docker --version
```

---

## 2. Criando um Cluster com Máquinas Virtuais

1. Crie 3 VMs no VirtualBox com Ubuntu Server ou Fedora Server.
2. Configure o modo de rede como **Rede Interna** ou **Adaptador de Rede Paravirtualizado (VirtIO)**.
3. Configure IPs estáticos em cada VM, por exemplo:

- VM1: 192.168.100.10  
- VM2: 192.168.100.11  
- VM3: 192.168.100.12

4. Teste o ping entre elas.

```bash
ping 192.168.100.11
```

---

## 3. Criando um Cluster de Containers (Docker Swarm)

### Passos:

```bash
# Em um dos nós (gerenciador)
docker swarm init --advertise-addr 192.168.100.10

# Nos outros nós (workers), use o token exibido:
docker swarm join --token <TOKEN> 192.168.100.10:2377

# Verifique os nós no gerenciador:
docker node ls
```

---

## 4. Vantagens e Desvantagens

| Critério               | Máquinas Virtuais              | Containers                      |
|------------------------|--------------------------------|----------------------------------|
| Desempenho            | Mais pesado                   | Mais leve e rápido              |
| Isolamento            | Total (inclui kernel)         | Parcial (compartilham kernel)   |
| Portabilidade         | Limitada                      | Alta (imagem leve)              |
| Tempo de Inicialização| Lento                         | Muito rápido                    |
| Uso de Recursos       | Mais recursos                 | Menos recursos                  |

---

## 5. Sistema Web Escalável com Containers

Estrutura:

- NGINX (Proxy reverso)
- Aplicação Web (Ex: Flask ou Node.js)
- Banco de Dados (Ex: PostgreSQL)

### Docker Compose: `docker-compose.yml`
#### docker compose atua como uma ferramenta que permite executar varias acoes tendo independente do SO da maquina.
```yaml
version: '3'

services:
  web:
    image: node:18
    volumes:
      - ./app:/app
    working_dir: /app
    command: node server.js
    ports:
      - "3000:3000"

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: appdb
    volumes:
      - pgdata:/var/lib/postgresql/data

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf

volumes:
  pgdata:
```

### nginx.conf
```
events {}
http {
    server {
        listen 80;
        location / {
            proxy_pass http://web:3000;
        }
    }
}
```

---

## 6. Gerenciamento de Containers

### Sugestão: Portainer

Instalação:
```bash
docker volume create portainer_data
docker run -d -p 9000:9000 --name portainer   --restart=always   -v /var/run/docker.sock:/var/run/docker.sock   -v portainer_data:/data   portainer/portainer-ce
```

Acesse: `http://localhost:9000`

---

## 7. Sistemas Operacionais com foco em Virtualização

1. **Proxmox VE**  
   - Foco em virtualização KVM e containers LXC.
   - Interface web integrada.

2. **Ubuntu Server com KVM**  
   - Suporte a libvirt, KVM e virt-manager.

3. **Fedora Server com Cockpit**  
   - Ferramenta web para administração, incluindo máquinas virtuais.

---

## Conclusão

Este projeto permite comparar tecnologias de virtualização e conteinerização e implementar um sistema web escalável baseado em microserviços.
