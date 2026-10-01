🍷 Vinheria Full — Check Point 5

FIAP — Check Point 5 | Monitoramento IoT aplicado a Vinherias

Projeto desenvolvido para o Check Point 5 da disciplina, com foco no monitoramento de temperatura, umidade e luminosidade de ambientes de armazenamento de vinhos utilizando ESP32, DHT, LDR e FIWARE, além de uma API para gerenciamento dos dispositivos, histórico das medições, configuração de triggers e acionamento remoto de alertas.

👥 Integrantes

Integrante

RM

Gabriel Souza Alexandre Silva

572607

Leonardo Formigari Fontes

573291

📌 1. Sobre o projeto

A Vinheria Full é uma solução de monitoramento IoT desenvolvida para acompanhar condições ambientais importantes para o armazenamento de vinhos.

O ESP32 realiza a coleta dos dados dos sensores e envia as informações para a infraestrutura FIWARE através de MQTT + Ultralight 2.0.

A plataforma recebe e organiza os dados, permitindo que o sistema trabalhe com:

🌡️ Temperatura;

💧 Umidade;

💡 Luminosidade;

🚨 Estado dos alertas;

📊 Histórico das medições;

⚙️ Triggers configuráveis;

📡 Cadastro e gerenciamento dos dispositivos;

🔊 Alertas sonoros;

🔵 Alerta visual através do LED azul do ESP32.

O sistema foi projetado para que a decisão de ativar um alerta seja realizada pelo Front-end, de acordo com os limites configurados para cada dispositivo.

🎯 2. Objetivo

O objetivo da Vinheria Full é disponibilizar uma solução IoT capaz de:

Monitorar continuamente as condições ambientais;

Enviar os dados dos sensores para o FIWARE;

Armazenar e consultar dados históricos;

Permitir o cadastro e gerenciamento de dispositivos;

Configurar limites mínimos e máximos para temperatura, umidade e luminosidade;

Identificar situações fora dos parâmetros definidos;

Acionar remotamente o LED azul do ESP32;

Emitir diferentes padrões sonoros para cada tipo de anomalia;

Disponibilizar os dados para visualização através de um dashboard web.

🧠 3. Funcionamento da solução

O fluxo principal da solução é:

┌──────────────────────┐
│      Sensores        │
│                      │
│ DHT11 / DHT22        │
│ LDR                  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│        ESP32         │
│                      │
│ Leitura dos sensores │
└──────────┬───────────┘
           │
           │ MQTT / UL20
           ▼
┌──────────────────────┐
│     IoT Agent UL     │
│       FIWARE         │
└──────────┬───────────┘
           │ NGSIv2
           ▼
┌──────────────────────┐
│        Orion         │
│                      │
│ Estado atual         │
└──────────┬───────────┘
           │
           │ Subscription
           ▼
┌──────────────────────┐
│     STH-Comet        │
│       :8666          │
│                      │
│ Histórico            │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│       Backend        │
│       Flask          │
│                      │
│ API REST + SQLite    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│      Dashboard       │
│        Web           │
│                      │
│ Triggers / Gráficos  │
│ Dispositivos / Alertas│
└──────────────────────┘

🏗️ 4. Arquitetura em camadas

A solução foi organizada em camadas para facilitar manutenção, evolução e separação de responsabilidades.

Camada 1 — Hardware / Edge

Responsável pela coleta e execução dos comandos.

Componentes:

ESP32 DevKit;

DHT11 no hardware real;

DHT22 na simulação Wokwi;

LDR;

Buzzer;

LED azul;

Wi-Fi.

O firmware realiza a leitura dos sensores e envia os dados através do MQTT.

Camada 2 — Comunicação

A comunicação entre o ESP32 e o FIWARE utiliza:

MQTT;

FIWARE IoT Agent UL;

Ultralight 2.0.

Exemplo de mensagem enviada pelo dispositivo:

t|23.5|h|65.0|l|40|a|temp,hum

Onde:

t = temperatura;

h = umidade;

l = luminosidade;

a = estado dos alertas.

Camada 3 — Plataforma FIWARE

A infraestrutura FIWARE é responsável pelo gerenciamento dos dados IoT.

IoT Agent

Responsável pela comunicação entre MQTT e FIWARE.

Porta utilizada:

4041

Orion Context Broker

Responsável pelo estado atual das entidades.

Porta:

1026

STH-Comet

Responsável pelo armazenamento e consulta dos dados históricos.

API:

8666

Fluxo:

ESP32
  ↓
MQTT
  ↓
IoT Agent
  ↓
Orion
  ↓
STH-Comet
  ↓
Histórico

Camada 4 — Backend / API

O backend foi desenvolvido em Python + Flask.

Responsabilidades:

comunicação com FIWARE;

cadastro de dispositivos;

atualização dos dispositivos;

exclusão dos dispositivos;

consulta da última leitura;

consulta do histórico;

gerenciamento dos triggers;

acionamento dos alertas;

registro dos alertas;

verificação de saúde dos serviços FIWARE.

Arquivos principais:

backend/
├── app.py
├── config.py
├── fiware_client.py
├── storage.py
└── requirements.txt

Camada 5 — Persistência

O projeto utiliza SQLite para armazenar dados administrativos do dashboard.

São armazenados:

dispositivos;

nome do dispositivo;

localização;

tipo de vinho;

triggers;

registro dos alertas.

As medições IoT permanecem sob responsabilidade da infraestrutura FIWARE/STH-Comet.

Camada 6 — Front-end / Dashboard

O backend está preparado para disponibilizar um dashboard web através da pasta:

frontend/

O dashboard previsto pela arquitetura permite:

cadastro de dispositivos;

gerenciamento dos dispositivos;

visualização da última leitura;

gráficos históricos;

configuração dos triggers;

acompanhamento dos alertas;

acionamento remoto dos atuadores.

Observação: o ZIP parcial utilizado para esta documentação contém o backend e o firmware, mas não contém a pasta frontend/. Portanto, a implementação final do dashboard deve ser adicionada ao repositório quando estiver disponível.

🌡️ 5. Sensores e atuadores

DHT11 / DHT22

O sensor DHT é utilizado para medir:

temperatura;

umidade.

No hardware real:

DHT11

Na simulação Wokwi:

DHT22

O firmware possui uma configuração para alternar entre os dois modelos.

#define USE_DHT22 1

Para hardware real com DHT11:

#define USE_DHT22 0

💡 LDR

O LDR é utilizado para medir a luminosidade do ambiente.

O valor analógico é convertido para uma escala percentual de:

0% → 100%

O firmware realiza uma média de leituras para reduzir ruídos do sensor.

🔵 LED azul

O LED azul é utilizado como indicador visual de anomalia.

Quando existir um alerta ativo:

LED → piscando

Quando todos os parâmetros retornarem ao normal:

LED → desligado

Pino utilizado:

GPIO 2

🔊 Buzzer

O buzzer fornece um alerta sonoro diferente para cada tipo de anomalia.

Temperatura

Padrão:

2 bipes agudos

Umidade

Padrão:

3 bipes curtos

Luminosidade

Padrão:

1 bipe longo

Caso existam múltiplas anomalias simultaneamente, o firmware alterna os padrões para que os diferentes tipos permaneçam identificáveis.

Pino:

GPIO 18

⚙️ 6. Triggers

Os triggers representam os limites mínimos e máximos permitidos para cada parâmetro.

O projeto possui presets de acordo com o tipo de vinho.

🍷 Vinho tinto

Temperatura: 14°C → 18°C
Umidade:     60% → 80%
Luminosidade: 0% → 30%

🥂 Vinho branco

Temperatura: 8°C → 12°C
Umidade:     60% → 80%
Luminosidade: 0% → 30%

🥂 Espumante

Temperatura: 6°C → 10°C
Umidade:     65% → 80%
Luminosidade: 0% → 25%

🍷 Genérico

Temperatura: 10°C → 16°C
Umidade:     60% → 80%
Luminosidade: 0% → 30%

Os valores podem ser ajustados através da API e, na versão final, pelo dashboard.

🚨 7. Sistema de alertas

A lógica de alerta foi planejada para ser controlada pelo Front-end.

O fluxo é:

Sensor
   ↓
FIWARE
   ↓
Dashboard
   ↓
Comparação com Trigger
   ↓
Valor fora do limite?
   │
   ├── NÃO → funcionamento normal
   │
   └── SIM
        ↓
     API de alerta
        ↓
       Orion
        ↓
     IoT Agent
        ↓
       MQTT
        ↓
       ESP32
        ↓
   ┌────┴─────┐
   ↓          ↓
 LED        Buzzer
 azul       específico

Quando o valor volta ao intervalo normal, o Front-end pode enviar:

{
  "alerts": []
}

O ESP32 então:

desliga o LED;

silencia o buzzer;

retorna ao funcionamento normal.

📡 8. Comunicação MQTT

O firmware utiliza os seguintes tópicos:

/TEF/<DEVICE_ID>/attrs
/TEF/<DEVICE_ID>/cmd
/TEF/<DEVICE_ID>/cmdexe

Publicação de sensores

/TEF/<DEVICE_ID>/attrs

Exemplo:

t|23.5|h|65.0|l|40|a|none

Recebimento de comandos

/TEF/<DEVICE_ID>/cmd

Exemplo:

vinheria001@alert|temp,hum

Confirmação

/TEF/<DEVICE_ID>/cmdexe

Exemplo:

vinheria001@alert|OK

🔌 9. Pinagem do ESP32

Componente

GPIO

DHT

GPIO 15

LDR

GPIO 34

Buzzer

GPIO 18

LED azul

GPIO 2

🧩 10. Estrutura do projeto

vinheria-full/
│
├── backend/
│   ├── app.py
│   ├── config.py
│   ├── fiware_client.py
│   ├── storage.py
│   └── requirements.txt
│
├── firmware/
│   └── vinheria_esp32/
│       ├── vinheria_esp32.ino
│       ├── libraries.txt
│       └── diagram.json
│
└── frontend/
    └── ...

A pasta frontend/ está prevista no backend, porém não estava presente no ZIP parcial utilizado para esta documentação.

🚀 11. Instalação do Backend

Pré-requisitos

Instalar:

Python 3.10 ou superior;

FIWARE;

Orion Context Broker;

IoT Agent UL;

STH-Comet;

MongoDB, conforme a infraestrutura FIWARE utilizada;

Broker MQTT/Mosquitto;

Arduino IDE ou ambiente compatível para o ESP32.

Criar ambiente virtual

Dentro da pasta backend:

Windows

python -m venv venv
venv\Scripts\activate

Linux / macOS

python3 -m venv venv
source venv/bin/activate

Instalar dependências

pip install -r requirements.txt

Dependências principais:

Flask
flask-cors
requests

▶️ 12. Executando o Backend

Entre na pasta:

cd backend

Execute:

python app.py

Por padrão:

http://localhost:5000

Quando o FIWARE estiver em outra máquina ou VM:

FIWARE_HOST=<IP_DA_VM> python app.py

🌐 13. API REST

Health Check

GET /api/health

Verifica:

Orion;

IoT Agent;

STH-Comet.

Bootstrap

POST /api/bootstrap

Responsável por criar/configurar:

service group;

subscription Orion → STH-Comet.

Presets

GET /api/presets

Retorna os triggers sugeridos para cada tipo de vinho.

Listar dispositivos

GET /api/devices?with_latest=1

Cadastrar dispositivo

POST /api/devices

Atualizar dispositivo

PUT /api/devices/<id>

Remover dispositivo

DELETE /api/devices/<id>

Última leitura

GET /api/devices/<id>/latest

Histórico

GET /api/devices/<id>/history

Exemplo:

/api/devices/vinheria001/history?lastN=50

O histórico utiliza o STH-Comet na porta 8666.

Consultar triggers

GET /api/devices/<id>/triggers

Alterar triggers

PUT /api/devices/<id>/triggers

Acionar alerta

POST /api/devices/<id>/alert

Exemplo:

{
  "alerts": ["temp", "hum"],
  "detail": "Temperatura e umidade fora dos limites"
}

Para normalizar:

{
  "alerts": []
}

Histórico de alertas

GET /api/devices/<id>/alert-log

🔐 14. Configuração

As principais configurações ficam no arquivo:

backend/config.py

Também podem ser sobrescritas por variáveis de ambiente.

Exemplo:

FIWARE_HOST=192.168.0.100

Principais serviços:

Orion:
http://<FIWARE_HOST>:1026

IoT Agent:
http://<FIWARE_HOST>:4041

STH-Comet:
http://<FIWARE_HOST>:8666

Configurações FIWARE utilizadas pelo projeto:

FIWARE_SERVICE=smart
FIWARE_SERVICEPATH=/
API_KEY=TEF
ENTITY_TYPE=Vinheria

🔧 15. Configuração do ESP32

Arquivo:

firmware/vinheria_esp32/vinheria_esp32.ino

Configurações principais:

const char* WIFI_SSID = "Wokwi-GUEST";
const char* WIFI_PASS = "";

const char* MQTT_BROKER = "0.0.0.0";
const uint16_t MQTT_PORT = 1883;

const char* DEVICE_ID = "vinheria001";
const char* API_KEY = "TEF";

⚠️ Atenção

Antes de utilizar o hardware real, alterar:

MQTT_BROKER

para o endereço IP real do broker MQTT/FIWARE.

Também é necessário configurar:

WIFI_SSID
WIFI_PASS

com a rede Wi-Fi utilizada no laboratório.

🧪 16. Simulação no Wokwi

O projeto possui o arquivo:

firmware/vinheria_esp32/diagram.json

A simulação contém:

ESP32 DevKit;

DHT22;

LDR;

buzzer;

LED azul;

resistor.

Componentes da simulação

ESP32
 ├── DHT22
 ├── LDR
 ├── Buzzer
 └── LED Azul

Link do Wokwi: adicionar aqui o link final da simulação.

🛠️ 17. Hardware real

Para o hands-on, utilizar:

ESP32
DHT11
LDR
Buzzer
LED azul

O firmware possui suporte para DHT11 através da configuração:

#define USE_DHT22 0

📊 18. Dashboard

O dashboard deverá concentrar as principais funções da solução:

Dispositivos

cadastrar dispositivo;

editar dispositivo;

remover dispositivo;

visualizar localização;

selecionar tipo de vinho.

Monitoramento

temperatura atual;

umidade atual;

luminosidade atual;

estado dos alertas.

Histórico

gráfico de temperatura;

gráfico de umidade;

gráfico de luminosidade.

Triggers

temperatura mínima;

temperatura máxima;

umidade mínima;

umidade máxima;

luminosidade mínima;

luminosidade máxima.

Alertas

temperatura;

umidade;

luminosidade.

📈 19. Dados históricos

O histórico é obtido através do STH-Comet:

http://<FIWARE_HOST>:8666

O backend consulta individualmente:

temperature
humidity
luminosity

e retorna os dados para consumo do dashboard.

Isso permite que os gráficos sejam alimentados por dados reais provenientes da infraestrutura FIWARE.

🗂️ 20. Persistência local

O sistema utiliza SQLite para dados administrativos.

Banco:

vinheria.db

Tabelas:

devices
triggers
alert_log

devices

Armazena informações dos dispositivos.

triggers

Armazena os limites de temperatura, umidade e luminosidade.

alert_log

Mantém o histórico dos alertas enviados pelo sistema.

🔄 21. Ciclo de funcionamento

1. ESP32 inicia
        ↓
2. Conecta ao Wi-Fi
        ↓
3. Conecta ao MQTT
        ↓
4. Realiza leitura dos sensores
        ↓
5. Envia dados ao IoT Agent
        ↓
6. IoT Agent envia dados ao Orion
        ↓
7. Orion mantém o estado atual
        ↓
8. STH-Comet armazena o histórico
        ↓
9. Dashboard consulta os dados
        ↓
10. Front-end compara com os triggers
        ↓
11. Se houver anomalia, envia comando
        ↓
12. Orion → IoT Agent → MQTT → ESP32
        ↓
13. LED azul pisca + buzzer específico
        ↓
14. Ao normalizar, o Front-end envia comando
        ↓
15. LED e buzzer são desligados

📋 22. Manual de operação

1. Iniciar a infraestrutura FIWARE

Certifique-se de que os serviços estejam funcionando:

Orion
IoT Agent
STH-Comet
MongoDB
MQTT Broker

2. Iniciar o backend

cd backend
python app.py

3. Configurar o ESP32

Defina:

WIFI_SSID
WIFI_PASS
MQTT_BROKER
DEVICE_ID

4. Cadastrar o dispositivo

Utilize o dashboard ou endpoint:

POST /api/devices

5. Verificar as leituras

Consulte:

GET /api/devices/<id>/latest

6. Consultar o histórico

Utilize:

GET /api/devices/<id>/history

7. Configurar os triggers

Ajuste os limites através do dashboard.

8. Testar um alerta

Faça com que uma leitura ultrapasse um dos limites configurados.

O Front-end deverá enviar o alerta ao backend.

9. Verificar o ESP32

O dispositivo deverá:

piscar o LED azul;

emitir o padrão sonoro correspondente.

10. Retornar ao estado normal

Quando o parâmetro estiver novamente dentro do intervalo, o dashboard deverá limpar o alerta.

O ESP32 deverá:

desligar o LED;

parar o buzzer.

🧪 23. Checklist para o Hands-on

Antes da apresentação:

ESP32 funcionando;

DHT11 conectado;

LDR conectado;

Buzzer conectado;

LED azul funcionando;

Wi-Fi configurado;

MQTT funcionando;

IoT Agent funcionando;

Orion funcionando;

STH-Comet funcionando;

Backend funcionando;

Dashboard funcionando;

Dispositivo cadastrado;

Histórico sendo gerado;

Triggers configurados;

Alerta de temperatura testado;

Alerta de umidade testado;

Alerta de luminosidade testado;

LED piscando durante anomalia;

Buzzer emitindo padrões diferentes;

Alerta sendo encerrado quando o valor normaliza.

📚 24. Tecnologias utilizadas

Hardware

ESP32

DHT11

DHT22 — simulação

LDR

Buzzer

LED azul

Firmware

C++

Arduino Framework

Wi-Fi

MQTT

PubSubClient

DHT Sensor Library

Adafruit Unified Sensor

Backend

Python

Flask

Flask-CORS

Requests

SQLite

IoT / Cloud

FIWARE

Orion Context Broker

IoT Agent UL

STH-Comet

MongoDB

MQTT

Simulação

Wokwi

📁 25. Arquivos principais

Arquivo

Função

backend/app.py

API REST e servidor da aplicação

backend/config.py

Configurações do sistema

backend/fiware_client.py

Integração com FIWARE

backend/storage.py

Persistência SQLite

backend/requirements.txt

Dependências Python

firmware/vinheria_esp32/vinheria_esp32.ino

Firmware ESP32

firmware/vinheria_esp32/libraries.txt

Bibliotecas do firmware

firmware/vinheria_esp32/diagram.json

Circuito Wokwi

🎥 26. Evidências

Adicionar nesta seção:

📸 Dashboard

![Dashboard](assets/dashboard.png)

🔌 Hardware

![Hardware](assets/hardware.jpg)

🧪 Wokwi

Adicionar o link da simulação.

🎬 Vídeo do projeto

Adicionar o link do vídeo do Hands-on/Pitch.

🏆 27. Diferencial da solução

A solução combina monitoramento IoT com gerenciamento de parâmetros específicos para diferentes tipos de vinho.

Um dos principais diferenciais técnicos é o fluxo de alerta:

Dashboard
    ↓
API
    ↓
Orion
    ↓
IoT Agent
    ↓
MQTT
    ↓
ESP32

O ESP32 não precisa decidir sozinho quando existe uma anomalia. O Front-end compara os dados recebidos com os triggers configurados e envia o comando ao dispositivo quando necessário.

Além disso, diferentes padrões sonoros permitem distinguir:

🌡️ Temperatura
💧 Umidade
💡 Luminosidade

mesmo quando mais de um alerta estiver ativo.

📌 28. Status do projeto

Implementado na base enviada

Firmware ESP32

DHT11/DHT22

LDR

Buzzer

LED azul

MQTT

FIWARE IoT Agent

Orion

STH-Comet

API Flask

Cadastro de dispositivos via API

Atualização de dispositivos

Exclusão de dispositivos

Consulta da última leitura

Consulta do histórico

Configuração de triggers

Sistema de alertas

Registro de alertas

Simulação Wokwi

Para completar a entrega final

Adicionar/validar o Front-end final

Adicionar gráficos dinâmicos

Adicionar screenshots do dashboard

Adicionar link final do Wokwi

Adicionar vídeo da apresentação

Adicionar evidências do hardware real

Validar o hands-on no laboratório

👨‍💻 Autores

Gabriel Souza Alexandre Silva
RM 572607

Leonardo Formigari Fontes
RM 573291

🍷 Vinheria Full

FIAP — Check Point 5 — 2026

Monitoramento inteligente para ambientes de armazenamento de vinhos através de IoT, FIWARE e análise de dados.
