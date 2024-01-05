# Attacktive-Directory


#TASK 3

Questions

nmap -n -T4 -sCV $TARGET_IP

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/48b2485d-b7e0-4bbc-a3e2-d9d8b87a8fa6)

1 - Qual ferramenta nos permitirá enumerar a porta 139/445?
enum4linux

enum4linux -U $TARGET_IP

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/9b888e47-7d55-4d9b-84b6-4f178c34f684)

2 - Qual é o nome de domínio NetBIOS da máquina?
THM-AD

Na imagem abaixo podemos observar que não tem um domínio, então a máquina é local.

![image](https://github.com/yanfernandess/Attacktive-Directory/assets/100174458/e30c3c0c-8bd7-4da3-8417-53b0e0004e3b)

3 - Qual TLD inválido as pessoas costumam usar para seu domínio do Active Directory?
.local

