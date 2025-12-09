# Ponderada - Segurança em Iot

## Grupo 2 - Turma 15

### Contribuição Individual:
- **Emanuelly Dias** - Identificação do ataque 2
- **Felipe Neves** - Montagem do protótipo físico e testagem
- **Livia Cavalcanti** - Organização do repositório
- **Maria Arielly** - Auxílio na identificação do ataque 1
- **Nicolli Santana** - Auxílio na identificação do ataque 2
- **Rafael Santana** - Identificação do ataque 1 e organização do relatório
- **Vinicius Maciel** - Organização da tabela de ataques

### Relatório técnico:

# 1. Introdução

Este relatório apresenta uma análise de segurança baseada no código do projeto **ESP32 Web Server**, criado com a Arduino IDE. O sistema consiste em um servidor HTTP simples capaz de receber requisições GET para controlar GPIOs (ex.: ligar/desligar LEDs, acionamento de relés). A análise considera vulnerabilidades típicas de projetos IoT e foca em dois ataques principais retirados da planilha fornecida.

# 2. Vulnerabilidades Identificadas

Durante a análise estática do código e arquitetura, as seguintes vulnerabilidades foram observadas no projeto:

## 2.1 Comunicação não segura

- O servidor utiliza HTTP sem criptografia (porta 80). Dessa forma, qualquer pessoa que obtiver o firmware (ou mesmo uma impressão serial) descobre sua rede, pois as credenciais de Wi-Fi estão expostas.
- Dados trafegam em texto plano, podendo ser interceptados por qualquer agente malicioso com acesso à rede, pois não há criptografia ou alguma forma de proteção no código.

## 2.2 Autenticação fraca

- O sistema não implementa qualquer autenticação.
- Apesar de haver sistema de autenticação, essa proteção é insuficiente para impedir que invasores entrem.
- Qualquer cliente capaz de identificar o IP do ESP32 pode enviar comandos como `/?led=on` ou `/?led=off`.
- Isso permite controle total do dispositivo sem barreiras.

Essas vulnerabilidades possibilitam ataques reais e relativamente fáceis de executar, descritos a seguir.

# 3. Possíveis Ataques

## Ataque 1 - Interceptação e Modificação de Dados

**Passo a passo do ataque:**

1. O atacante se conecta à mesma rede Wi-Fi que o ESP32 (ou cria uma rede clone).
2. Captura o tráfego HTTP usando ferramentas como Wireshark, tcpdump ou mitmproxy.
3. Interpreta facilmente as URLs, já que tudo está em texto plano (ex.: `/?led=on`).
4. O atacante pode modificar requisições ou reenviá-las à vontade para alterar o comportamento do sistema.
5. Caso esteja usando um proxy malicioso, pode alterar comandos antes que cheguem ao ESP32.

**Probabilidade:** Média a Alta  
**Justificativas:**

- Redes Wi-Fi compartilhadas (escola, empresa, condomínio) facilitam acesso.
- Tráfego não criptografado facilita interceptação.
- Ataque requer ferramentas simples, disponíveis publicamente.

**Impacto:** Alto  
Dependendo da função do ESP32, o impacto pode envolver:

- Controle indevido de atuadores (relés, motores, bombas, fechaduras).
- Alteração de dados sensíveis enviados por sensores.
- Possibilidade de causar danos físicos ou operacionais.

**Risco:** Moderado  
O sistema simples reduz o interesse de interceptação, mas a exposição das credenciais Wi-Fi agrava o problema.

## Ataque 2 - Acesso Não Autorizado ao Endpoint /login

**Passo a passo do ataque:**

1. O atacante descobre o endpoint `/login` analisando o tráfego HTTP ou testando URLs comuns.
2. Envia requisições diretamente ao endpoint usando navegador, curl ou ferramentas como Postman.
3. Como não há autenticação real, qualquer pessoa consegue acessar o endpoint.
4. O atacante força estados do sistema enviando parâmetros arbitrários (ex.: `/login?auth=1`).
5. Caso haja funções sensíveis atrás desse endpoint, ele consegue acioná-las sem qualquer controle.

**Probabilidade:** Alta  
**Justificativas:**

- Endpoint acessível sem autenticação.
- Basta saber o IP do ESP32 para acessar qualquer rota.
- Ataque exige conhecimento técnico mínimo.

**Impacto:** Alto  
Dependendo da função do endpoint, o impacto pode envolver:

- Acesso total às funcionalidades restritas do sistema.
- Execução de comandos administrativos sem controle.
- Possibilidade de comprometimento total do dispositivo ou do ambiente onde ele opera.

**Risco:** Muito Alto  
Este é um dos ataques mais graves possíveis em dispositivos IoT sem autenticação.

# Conclusão do Projeto

A avaliação do projeto **ESP32 Web Server** evidencia vulnerabilidades críticas decorrentes do uso de HTTP sem criptografia e da ausência de autenticação. Esses fatores permitem que atacantes explorem o sistema com facilidade, principalmente por meio de dois vetores: a interceptação e modificação de dados e o acesso não autorizado ao dispositivo.

A primeira falha possibilita que o tráfego seja capturado e alterado em redes compartilhadas, enquanto a segunda permite que qualquer indivíduo na mesma rede assuma controle total do ESP32 apenas enviando requisições simples.

Os impactos potenciais são elevados, sobretudo quando o dispositivo controla atuadores físicos, como relés, motores ou sistemas de segurança. Nessas situações, um ataque pode comprometer a integridade, a disponibilidade e até a segurança operacional do ambiente.

Diante da alta probabilidade de exploração e do risco associado, a adoção de medidas de mitigação torna-se indispensável. Entre as ações recomendadas estão:

- Implementação de autenticação robusta
- Uso de criptografia (HTTPS/TLS ou VPN)
- Isolamento adequado da rede
- Logs e auditorias
- Mecanismos de limitação de requisições

Essas estratégias reduzem significativamente a superfície de ataque e fortalecem a segurança geral do sistema, tornando o ESP32 mais resistente a incidentes e adequado para ambientes reais de operação.

### Tabela de ataques:
https://docs.google.com/spreadsheets/d/1Eh5pM_ZOMaPL8-UGehoX55APBAjksL9LzZquORMWxwM/edit?usp=sharing 

### Vídeo da montagem e testagem:
https://www.youtube.com/shorts/hCaKbR_SEyM 
