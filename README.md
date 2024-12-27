# Projeto AWS-Linux Compass | DevSecOps 2024

**Prática DevSecOps - Compass UOL PB 2024**

Este projeto apresenta um sistema prático e automatizado para monitorar o estado de um servidor Nginx em execução em um ambiente Linux, configurado no Windows Subsystem for Linux (WSL). O objetivo é demonstrar habilidades essenciais como configuração de ambiente, criação de scripts para automação e uso de cron para agendamentos periódicos.

---

## Índice

1. [Introdução](#introdução)
2. [Pré-requisitos](#pré-requisitos)
3. [Objetivos](#objetivos)
4. [Configuração do Ambiente](#configuração-do-ambiente)
   - [Habilitar WSL no Windows](#habilitar-wsl-no-windows)
   - [Instalar o Ubuntu](#instalar-o-ubuntu)
5. [Configuração do Servidor Nginx](#configuração-do-servidor-nginx)
6. [Criação e Teste do Script de Monitoramento](#criação-e-teste-do-script-de-monitoramento)
   - [Configuração de Diretórios e Logs](#configuração-de-diretórios-e-logs)
   - [Criar o Script de Monitoramento](#criar-o-script-de-monitoramento)
7. [Automatização do Script com Crontab](#automatização-do-script-com-crontab)
8. [Conclusão](#conclusão)

---

## Introdução

Este projeto ilustra a criação de um ambiente de monitoramento automatizado que verifica periodicamente o estado de um servidor Nginx. A solução foi desenvolvida com foco em aprendizado prático e aplicação em ambientes de produção. 

---

## Pré-requisitos

- Windows 10 ou superior com suporte ao WSL.
- WSL configurado com Ubuntu 20.04 LTS ou superior.
- Acesso administrativo ao sistema para configurar o WSL e permissões de script.
- Maquina capaz de rodar Windows 10 e armazenamento suficiente (Cerca de 50GBs)
---

## Objetivos

- Configurar um subsistema Ubuntu 20.04 ou superior no Windows.
- Subir um servidor Nginx e mantê-lo em execução.
- Criar um script para verificar o estado do serviço e gerar logs.
- Automatizar o monitoramento com agendamentos periódicos usando cron.

---

## Configuração do Ambiente

### Habilitar WSL no Windows

1. Abra o Prompt de Comando (CMD) ou PowerShell como administrador.
2. Execute o comando:
   ```bash
   wsl --install
3. Reinicie a Máquina.

### Instalar o Ubuntu
1. Execute o comando:
   ```bash
   wsl --install -d Ubuntu-20.04
2. Configure o nome de usuário e senha quando solicitado.

Agora com o ambiente devidamente configurado, partiremos para a configuração do servidor nginx
Lembrando, tudo isso será dentro do Linux (Ubuntu Baixado por WSL):
## Configuração do Servidor Nginx
1. Instale o Nginx:
    ```bash
    sudo apt install nginx
2. Inicie o serviço Nginx:
    ```bash
    sudo systemctl start nginx
3. Para testar o servidor, acesse http://localhost

## Criação e Teste do Script de Monitoramento  

### Configuração de Diretórios e Logs  

1. Crie o diretório para armazenar os logs:  
   ```bash
   sudo mkdir -p /var/log/nginx_monitor/logs
2. Configure as permissões:
   ```bash
   sudo chmod -R 755 /var/log/nginx_monitor
3. Crie arquivos de Log
   ```bash
    sudo touch /var/log/nginx_monitor/logs/online.log
    sudo touch /var/log/nginx_monitor/logs/offline.log

## Criar o Script de Monitoramento 

1. Crie o diretório para armazenar o script:
    ```bash
    sudo mkdir -p /var/log/nginx_monitor/scripts

2. Crie o arquivo do script:
    ```bash
    sudo nano /var/log/nginx_monitor/scripts/verificar_status.sh

3. Insira o seguinte conteúdo no script:
    ```bash
    #!/bin/bash

    ONLINE_LOG="/var/log/nginx_monitor/logs/online.log"
    OFFLINE_LOG="/var/log/nginx_monitor/logs/offline.log"
    SERVICO="Nginx"
    DATA_HORA=$(date "+%Y-%m-%d %H:%M:%S")
    
    if systemctl is-active --quiet nginx; then
        STATUS="ONLINE"
        MENSAGEM="$DATA_HORA | Serviço: $SERVICO | Status: $STATUS | Mensagem: O serviço está funcionando normalmente."
        echo "$MENSAGEM" >> "$ONLINE_LOG"
    else
        STATUS="OFFLINE"
        MENSAGEM="$DATA_HORA | Serviço: $SERVICO | Status: $STATUS | Mensagem: O serviço está fora do ar."
        echo "$MENSAGEM" >> "$OFFLINE_LOG"
    fi

4. Torne o script executável:

    ```bash
    sudo chmod +x /var/log/nginx_monitor/scripts/verificar_status.sh

## Automatização do Script com Crontab

1. Abra o editor do crontab:
    ```bash
    sudo crontab -e

2. Adicione a seguinte linha no final do arquivo para agendar a execução a cada 5 minutos:
    ```bash
    */5 * * * * /var/log/nginx_monitor/scripts/verificar_status.sh
3. Verifique a automação após 5 minutos:
    ```bash
    cat /var/log/nginx_monitor/logs/online.log
    cat /var/log/nginx_monitor/logs/offline.log

## Conclusão
Este projeto mostrou como configurar e automatizar o monitoramento de um servidor Nginx em um ambiente Linux via WSL. Abordamos desde a instalação e configuração do Nginx até a criação e automação de scripts para monitorar o serviço. A utilização do cron para agendamentos periódicos exemplifica uma solução prática e eficiente para garantir a disponibilidade do serviço, integrando conceitos de automação e segurança no processo.
