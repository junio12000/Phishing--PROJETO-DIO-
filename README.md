# Projeto de Conscientização em Phishing com Kali Linux e SET

## ⚠️ Aviso Importante

Este repositório documenta uma atividade acadêmica desenvolvida como parte do 
desafio de Cibersegurança da DIO (Digital Innovation One). Todo o processo foi 
executado em um **ambiente de laboratório isolado** (máquina virtual Kali Linux, 
rede local), utilizando **credenciais fictícias** e um **alvo de teste criado 
pelo próprio autor**. Nenhum dado real de terceiros foi capturado, armazenado 
ou exposto. O objetivo é exclusivamente educacional: compreender técnicas de 
engenharia social para melhor se defender delas.

## 🎯 Objetivo

Reproduzir, em ambiente controlado, um ataque de phishing clássico utilizando o 
Social-Engineer Toolkit (SET) para clonar uma página de login e demonstrar como 
credenciais podem ser capturadas por meio de engenharia social — evidenciando a 
importância de mecanismos de defesa como MFA, filtros anti-phishing e 
treinamento de usuários.

## 🛠️ Ferramentas Utilizadas

- **Kali Linux** (máquina virtual, modo bridge)
- **Social-Engineer Toolkit (SET)** v8.1.3
- **Apache2** (servidor web)
- Rede local (sem exposição à internet)
- Navegador de teste dentro da própria VM

## 📋 Metodologia

### 1. Tentativa inicial: clonagem do Facebook

O desafio original propõe clonar a página de login do Facebook. Durante os 
testes, o SET retornou o erro:

[*] Error. Unable to clone this specific site. Check your internet connection.


**Análise técnica:** grandes plataformas como o Facebook implementam mecanismos 
anti-clonagem em múltiplas camadas — renderização via JavaScript dinâmico, 
detecção de user-agent/bot no servidor e políticas de segurança (HSTS) — que 
impedem ferramentas de clonagem simples (como o site cloner do SET, baseado em 
requisições HTTP estáticas) de replicar a página com sucesso. Esse resultado, 
por si só, é um achado relevante: demonstra que sites modernos e bem protegidos 
resistem a esse vetor de ataque básico.

### 2. Criação de um ambiente de teste controlado

Para viabilizar a demonstração prática do Credential Harvester, foi criada uma 
página de login simplificada, hospedada localmente na própria VM:

```html
<!DOCTYPE html>
<html>
<head><title>Login de Teste</title></head>
<body style="font-family:Arial; text-align:center; margin-top:100px;">
    <h2>Login - Ambiente de Teste</h2>
    <form method="POST" action="login.php">
        <input type="text" name="username" placeholder="Usuário"><br><br>
        <input type="password" name="password" placeholder="Senha"><br><br>
        <button type="submit">Entrar</button>
    </form>
</body>
</html>
```

A página foi servida via Apache2 (`/var/www/html/`), simulando um "site alvo" 
para o laboratório.

### 3. Execução do Credential Harvester

No SET, o fluxo utilizado foi:

1 - Social-Engineering Attacks
2 - Website Attack Vectors
3 - Credential Harvester Attack Method
4 - Site Cloner


- **IP para o POST back:** `127.0.0.1`
- **URL clonada:** `http://127.0.0.1`

Como o Apache já ocupava a porta 80, o próprio SET identificou o conflito e 
ofereceu desabilitar o Apache automaticamente para assumir a porta com seu 
servidor PHP nativo — opção aceita para permitir a captura direta das 
submissões do formulário.

### 4. Captura das credenciais

Ao acessar a página clonada e submeter dados fictícios, o terminal do SET 
exibiu a captura em tempo real:

[*] WE GOT A HIT! Printing the output:
POSSIBLE USERNAME FIELD FOUND: username=teste
POSSIBLE PASSWORD FIELD FOUND: password=123456!@


## 📸 Evidências

| Etapa | Descrição | Arquivo |
|---|---|---|
| 1 | Tentativa de clonagem do Facebook (erro retornado) | `images/01-erro-facebook.png` |
| 2 | Página de teste clonada e funcionando no navegador | `images/02-pagina-clonada.png` |
| 3 | Captura de credenciais no terminal do SET | `images/03-captura-credenciais.png` |

## 🔍 Análise e Aprendizados

- Plataformas de grande porte possuem defesas técnicas que dificultam a 
  clonagem automatizada de páginas (JS dinâmico, detecção de bots, HSTS).
- Sites menores, mal protegidos ou ambientes internos sem essas camadas de 
  defesa são significativamente mais vulneráveis a esse tipo de ataque.
- O sucesso do ataque depende inteiramente da vítima não perceber sinais de 
  alerta, como:
  - URL diferente da original (IP local em vez do domínio real)
  - Ausência de certificado HTTPS válido
  - Pequenas inconsistências visuais na página clonada

## 🛡️ Mitigações e Recomendações

- **Autenticação multifator (MFA)** — mesmo com a senha capturada, o atacante 
  não teria acesso sem o segundo fator.
- **Verificação de URL** — conferir sempre o domínio antes de inserir credenciais.
- **Extensões/anti-phishing** — navegadores e softwares de segurança que alertam 
  sobre páginas suspeitas.
- **Treinamento de conscientização** — a principal defesa contra engenharia 
  social é a capacitação do usuário final.
- **SPF/DKIM/DMARC** — no caso de phishing distribuído por e-mail, essas 
  configurações ajudam a evitar spoofing de domínio.

## 📚 Referências

- Documentação oficial do Social-Engineer Toolkit
- Material do curso de Cibersegurança — DIO

---

*Projeto desenvolvido para fins educacionais, como parte do desafio de 
Cibersegurança da DIO.*