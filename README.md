# Writeup_Medusa_Metasploitable
Olá! Aqui é o Caio (aka Choujin), e hoje farei um write up sobre o desafio da Dio para testes de penetração na máquina vulnerável Metasploitable 2, utilizando das seguintes tecnologias: 
- Kali Linux
- Medusa
- NMap
- Enum4Linux
- smbclient
  
---
### Primeira etapa: Configurando  o VB
Não me estenderei muito nessa etapa, visto que tudo que fiz foi baixar as respectivas imagens nos sites oficiais (no caso do Metasploitable, o SourceForge) e definir as configurações de rede de ambas as máquinas para host-only, pra garantir que ambas se comuniquem sem acesso à internet. 

### Segunda etapa: Varredura 
Aqui, utilizamos o comando: nmap -v [IP alvo]

nmap: chamado da ferramenta

-v: modo Verbose 

Passando pelo comando, obtivemoso o seguinte resultado: 

![varredura](https://github.com/user-attachments/assets/d875b826-b944-4044-9326-725d343d7dde)

Percebemos pelo resultado todos os serviços disponíveis, portas e a condição delas (no caso do Metasploitable, todas abertas)

### Terceira etapa: Enumeração
Aqui, utilizaremos do enum4linux com o seguinte comando: enum4linux -a [IP alvo] | tee exemplo.txt

enum4linux: chamado da ferramenta

-a: essa flag utiliza de todas as outras flags básicas de enumeração da ferramenta

|: pipe para separação dos comandos

tee: passa o output para um arquivo, nesse caso txt

Obtemos então a seguinte saída: 

![enumeraçao](https://github.com/user-attachments/assets/b9713ab1-5175-474d-acc7-d2bcde60bb6c)

Aqui obtivemos informações sobre usuários e serviços, informações que serão úteis mais tarde no desafio

### Quarta etapa: Ataques >:^)

Como dito no título do repositório, aqui utilizaremos da técnica de brute force (quebra de senhas à força bruta) com a ferramenta Medusa, uma das presentes no arsenal do Kali
Atacaremos primeiro o serviço ftp, mas antes, criaremos duas wordlists básicas pra usuários e senhas comuns usando o comando echo. Existem diversas wordlists na internet de todos os tamanhos possíveis, listas como a rockyou posssuem literais GIGAS apenas de palavras.

Criada as wordlists, prosseguiremos com o comando: medusa -h [IP alvo] -U exemplo_user.txt -P exemplo_pass.txt -M ftp -t 6

medusa: chamado da ferramenta

-h: hostname ou endereço IP do alvo, nesse caso IP

-U: lista de usernames que será utilizada

-P: lista de passwords que será utilizada

-M: módulo a ser utilizado, nesse caso o ftp

-t: número de threads a ser utilizada, 6 nesse caso para acelerar o processo

Ao passsar o comando, obtivemos:

![result_medusa_ftp](https://github.com/user-attachments/assets/05376fb3-9924-4365-b2b6-b330dd35c6af)

E ao testar as credenciais marcadas como [SUCCESS]: 

![ftp_success](https://github.com/user-attachments/assets/2d22a3d8-7780-4157-ab9b-7282d7bffce9)

Estamos dentro :^)

Seguindo adiante, agora atacaremos a página web DVWA (Damn Vulnerable Web App), com o seguinte comando: medusa -h [IP do alvo] -U exemplo_user.txt -P exemplo_pass.txt -M http -m PAGE: "/example/directory" -m FORM: "example form" -m FAIL: "example fail" -t 6

medusa: chamado da ferramenta

-h: hostname ou endereço IP do alvo, nesse caso IP

-U: lista de usernames que será utilizada

-P: lista de passwords que será utilizada

-M: módulo a ser utilizado, nesse caso o http

-m: parâmetros do módulo escolhido, nesse caso:
  - PAGE: parâmetro que pede qual o diretório alvo
  - FORM: formulário a ser atacado
  - FAIL: qual a mensagem de erro esperada

-t: número de threads a ser utilizada, 6 nesse caso para acelerar o processo

Aqui utilizaremos as mesmas wordlists do teste passado, e passando o comanddo: 

![http](https://github.com/user-attachments/assets/7565bc57-35e3-4f49-9eb0-7602819c3052)

Aqui obtivemos um resultado no mínimo curioso. Como pode ser observado, obtivemos diversos resultados marcados como [SUCCESS]. Não sei o que pode ter gerado isso, imagino que, depois que um dos resultados dá certo, talvez os outros também marquem como sucesso? who knows

Mas, após tentar algumas combinações das credenciais testadas, a que resulta na invasão é 
user: admin
passsword: password 

Finalizando nosso WriteUp, vamos agora atacar o serviço SMB (Server Message Block) com uma técnica de password spraying, que consiste basicamente em tentar uma senha commum em vários usuários ao invés de testar várias senhas num usuário só, o que geralmente levaria a um bloqueio de IP em sistemas minimamente protegiddos

Comeeçaremos, assim como no primeiro teste, criando wordlists, nesse caso wordlists simples utilizanddo do echo

Após criadas, utilizamos do comando: medusa -h [IP alvo] -U users_smb.txt -P senhas_spraying.txt -M smbnt -t 2 -T 50 

-h: hostname ou endereço IP do alvo, nesse caso IP

-U: lista de usernames que será utilizada

-P: lista de passwords que será utilizada

-M: módulo a ser utilizado, nesse caso o smb

-t: número de threads a ser utilizada, 6 nesse caso para acelerar o processo

-T: número de hosts a serem testados

Passando o comando; obtemos: 

![smbnt](https://github.com/user-attachments/assets/5f3abef2-b658-4443-95b0-298627311062)

Podemos ver a credencial certa pois ela muda de ACCOUNT CHECK pra ACCOUNT FOUND, além da tag de [SUCCESS] 

E testanddo as credenciais obtidas...

![smbnt_result](https://github.com/user-attachments/assets/c7e8ac43-7ed2-47f5-a239-6802b5854d01)

Dentro mais uma vez :^) 

Desafios bem interessantes, imagino que existem dezenas de outras invasões que possam ser testadas nos vários serviços do Metasploitable, e que serão testados em breve
Ciaao :^)))
