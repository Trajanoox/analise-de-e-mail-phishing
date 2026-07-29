# 📧 Análise de E-mail Phishing

## 🎯 Objetivo

O **phishing** é um dos vetores de ataque mais comuns, tanto contra grandes empresas quanto contra usuários comuns.

Por definição, phishing é uma técnica de engenharia social na qual o atacante se passa por uma empresa ou pessoa confiável por meio de comunicações fraudulentas, como e-mails, SMS, mensagens instantâneas ou até chamadas telefônicas, com o objetivo de roubar informações sensíveis da vítima.

Neste projeto, realizo a análise técnica de um e-mail de phishing, avaliando:

* Linguagem utilizada na mensagem;
* Protocolos de autenticação de e-mail (SPF, DKIM e DMARC);
* Cabeçalho SMTP;
* Endereço IP do remetente;
* Indicadores que permitem identificar a fraude.

---

## 🛠️ Habilidades Desenvolvidas

* Análise de e-mails de phishing
* Leitura e interpretação de cabeçalhos SMTP
* Análise dos protocolos SPF, DKIM e DMARC
* Investigação de endereços IP
* Desenvolvimento de análise técnica
* Documentação de incidentes de segurança

---

## 🔧 Ferramentas Utilizadas

* AbuseIPDB
* VirusTotal
* MXToolbox
* Base64 Decoder

---

# 🔍 Análise Técnica

## 1. Análise do Cabeçalho

Para facilitar a visualização do cabeçalho SMTP, utilizei a ferramenta **MXToolbox**.

Após copiar e colar todo o cabeçalho do e-mail na ferramenta, já é possível visualizar diversas informações importantes, principalmente o resultado dos protocolos de autenticação responsáveis por validar a origem da mensagem.

### Protocolos analisados

* SPF (Sender Policy Framework)
* DKIM (DomainKeys Identified Mail)
* DMARC (Domain-based Message Authentication, Reporting and Conformance)

Esses três mecanismos trabalham em conjunto para autenticar a origem de um e-mail e reduzir ataques de spoofing e phishing.

---

## 2. Estrutura do Cabeçalho

A ferramenta também organiza cada campo do cabeçalho na seção **Headers Found**, facilitando a interpretação das informações.

> **Imagem:** Headers Found

![headersfound](https://github.com/Trajanoox/analise-de-e-mail-phishing/blob/main/prints/headers%20found.png?raw=true
)

---

## 3. Authentication-Results

Entre todas as informações disponíveis, o campo **Authentication-Results** é um dos mais importantes durante uma investigação de phishing, pois apresenta o resultado das validações realizadas pelos protocolos SPF, DKIM e DMARC.

> **Imagem:** Authentication-Results

![authenticatorresults](https://github.com/Trajanoox/analise-de-e-mail-phishing/blob/main/prints/authentication.png?raw=true)

---

## 4. Análise do SPF

O protocolo **SPF (Sender Policy Framework)** verifica se o endereço IP utilizado para enviar o e-mail está autorizado pelo domínio do remetente.

Neste caso, foi identificado que:

* IP do remetente: **137.184.34.4**
* Domínio utilizado: **@atendimento.com.br**

O endereço IP **não possui autorização para enviar mensagens utilizando esse domínio**.

Também é possível observar que:

* O e-mail foi enviado pelo servidor:

```
ubuntu-s-1vcpu-1gb-35gb-intel-sfo3-06
```

* A validação SPF foi realizada pelo servidor do destinatário:

```
BN8NAM11FT066.mail.protection.outlook.com
```

Resultado:

❌ **SPF = FAIL**

---

## 5. Análise do DKIM

O **DKIM (DomainKeys Identified Mail)** é responsável por verificar a integridade da mensagem através de assinatura criptográfica.

No cabeçalho foi identificado:

```
dkim=none (message not signed)
```

Ou seja, a mensagem **não possui assinatura DKIM**, impossibilitando a verificação da sua integridade.

Resultado:

❌ **DKIM = NONE**

---

## 6. Análise do DMARC

O **DMARC (Domain-based Message Authentication, Reporting and Conformance)** funciona como uma camada adicional de proteção.

Para que a validação seja bem-sucedida, é necessário que:

* SPF ou DKIM sejam aprovados;
* O domínio autenticado esteja alinhado com o domínio apresentado no campo **From**.

Como SPF falhou e DKIM não está presente, o DMARC também falha.

Resultado:

❌ **DMARC = FAIL**

---

## 7. Investigação do Endereço IP

Além da análise do cabeçalho, foi realizada a verificação do endereço IP utilizando:

* VirusTotal
* AbuseIPDB

As duas ferramentas identificaram que o endereço IP é originário dos **Estados Unidos**.

Esse é um forte indicador de comprometimento, considerando que a mensagem tenta se passar pelo **Bradesco**, uma instituição financeira brasileira, sem justificativa para utilizar infraestrutura localizada em outro país.

> **Imagem:** AbuseIPDB

![abuseiIPDB](https://github.com/Trajanoox/analise-de-e-mail-phishing/blob/main/prints/abuseipdb.png?raw=true)

> **Imagem:** VirusTotal

![virustotal](https://github.com/Trajanoox/analise-de-e-mail-phishing/blob/main/prints/virustotal.png?raw=true
)

---

## 8. Engenharia Social

Após decodificar o conteúdo da mensagem utilizando um **Base64 Decoder**, foi possível observar outro forte indicador de phishing.

O texto utiliza técnicas clássicas de engenharia social, como:

* senso de urgência;
* pressão para ação imediata;
* incentivo ao clique em links;
* tentativa de induzir o usuário a fornecer informações.

Essas características são amplamente utilizadas para reduzir o tempo de reflexão da vítima e aumentar a probabilidade de sucesso do ataque.

---

# ✅ Veredito

A análise técnica identificou diversos indicadores que confirmam que a mensagem é um **e-mail de phishing**.

### Evidências encontradas

* ❌ SPF reprovado
* ❌ DKIM ausente
* ❌ DMARC reprovado
* 🌎 Endereço IP localizado nos Estados Unidos
* 🚩 Tentativa de personificação de instituição brasileira
* ⚠️ Linguagem com forte senso de urgência
* 🔗 Incentivo ao clique em links suspeitos

A combinação desses fatores permite concluir, com alto grau de confiança, que a mensagem analisada possui características típicas de uma campanha de phishing e não deve ser considerada confiável. Apresenta características compatíveis com a técnica T1566.002 – Spearphishing Link, uma vez que busca induzir a vítima a acessar um link por meio de engenharia social.
