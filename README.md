# 🍇 PROJETO VITRA: Monitoramento Inteligente de Adegas

O Projeto VITRA é um sistema Data Logger desenvolvido em Arduino/ATmega328P para monitoramento e registro automatizado de condições ambientais (temperatura, umidade e luminosidade) em salas de armazenamento e adegas de vinho. O equipamento conta com alertas visuais e sonoros, armazenamento não volátil em memória EEPROM, relógio em tempo real (RTC) e suporte a múltiplos padrões regionais.

---

## Sumário

- [Visão Geral](#-visão-geral)
- [Especificações Técnicas e Precisão dos Sensores](#-especificações-técnicas-e-precisão-dos-sensores)
- [Limites Operacionais e Triggers](#-limites-operacionais-e-triggers)
- [Esquema do Circuito e Diagrama de Ligações](#-esquema-do-circuito-e-diagrama-de-ligações)
- [Design e Encapsulamento (Gabinete 3D)](#-design-e-encapsulamento-gabinete-3d)
- [Manual de Operação e Guia de Uso (IHM)](#-manual-de-operação-e-guia-de-uso-ihm)
- [Estrutura da Memória EEPROM](#-estrutura-da-memória-eeprom)
- [Lista de Materiais (BOM)](#-lista-de-materiais-bom)
- [Código Fonte Completo](#-código-fonte-completo)
- [Como Compilar e Carregar](#-como-compilar-e-carregar)

---

## Visão Geral

O controle rigoroso das condições ambientais é fundamental na conservação do vinho. Variações bruscas de temperatura, umidade inadequada ou incidência de luz podem deteriorar o produto.

Este **Data Logger Ambiental** efetua a leitura periódica dos sensores e, sempre que detecta parâmetros fora da faixa segura, dispara alertas locais (LED RGB e Buzzer) e grava um registro contendo **data, hora, temperatura, umidade e luminosidade** na EEPROM interna do microcontrolador para auditoria e histórico.

---

## Especificações Técnicas e Precisão dos Sensores

| Componente | Função | Unidade de Medida | Faixa de Operação | Precisão / Resolução |
| :--- | :--- | :--- | :--- | :--- |
| **Microcontrolador** | ATmega328P / Arduino Uno | - | 16 MHz, 5V DC | EEPROM 1 KB, Flash 32 KB |
| ** DHT11** | Temperatura do Ar | Graus Celsius (°C) / Fahrenheit (°F) | -40 °C a +80 °C | ±0.5 °C / Resolução: 0.1 °C |
| **DHT11** | Umidade Relativa do Ar | Porcentagem (%RH) | 0% a 100% RH | ±2% a ±5% RH |
| **LDR + Resistor 10kΩ** | Luminosidade | Porcentagem Mapeada (%) | 0% (Escuro) a 100% (Luz Máxima) | Mapeamento ADC (10-bits, 0-1023) |
| **RTC DS1307** | Data e Hora Real | Timestamp Unix (s desde 1970) | Calendário completo até 2100 | Relógio de alta precisão via I2C |

---

## Limites Operacionais e Triggers

| Parâmetro | Faixa Segura (Mín - Máx) | Ação Fora da Faixa (Alerta) |
| :--- | :--- | :--- |
| **Temperatura** | 15 °C a 25 °C | Status Alerta + LED Vermelho Piscando + Buzzer (1500Hz) + Gravação EEPROM |
| **Umidade** | 30% a 50% | Status Alerta + LED Vermelho Piscando + Buzzer (1500Hz) + Gravação EEPROM |
| **Luminosidade** | 0% a 30% | Status Alerta + LED Vermelho Piscando + Buzzer (1500Hz) + Gravação EEPROM |

---

## Esquema do Circuito e Diagrama de Ligações

O circuito foi projetado e simulado na plataforma **Wokwi**, composto por:

### Tabela de Pinos do Microcontrolador

| Módulo / Periférico | Pino do Módulo | Pino no Arduino Uno |
| :--- | :--- | :--- |
| **Sensor DHT11** | Data (Sinal) | Digital `D2` |
| **Sensor LDR** | Sinal Analógico | Analógico `A0` |
| **LED RGB (Vermelho)** | Anodo / Catodo Red | Digital `D9` (PWM) |
| **LED RGB (Verde)** | Anodo / Catodo Green | Digital `D10` (PWM) |
| **LED RGB (Azul)** | Anodo / Catodo Blue | Digital `D11` (PWM) |
| **Buzzer** | Positivo (+) | Digital `D8` |
| **Display LCD 16x2 I2C** | SDA / SCL | Analógico `A4` / `A5` |
| **RTC DS1307 (I2C)** | SDA / SCL | Analógico `A4` / `A5` |
| **Keypad 4x4 (Linhas R1-R4)** | Row 1..4 | Digital `D3`, `D5`, `D6`, `D7` |
| **Keypad 4x4 (Colunas C1-C4)** | Col 1..4 | Digital `D12`, `D13`, `A1`, `A2` |

---

## Design e Encapsulamento (Gabinete 3D)

O encapsulamento do equipamento foi idealizado com uma forte identidade visual inspirada na viticultura:

- **Formato:** Cacho de Uva com acabamento em PLA/PETG (Cores Roxo e Verde).
- **Dimensões:** ~190 mm (Altura) x ~150 mm (Largura) x ~40 mm (Profundidade).
- **Painel Frontal:** Recortes dedicados para o visor do Display LCD 16x2 (68x36 mm) e para o Teclado Matricial 4x4 (76x69 mm).
- **Espaço Interno:** Nicho traseiro com fecho por parafusos, acomodando a Protoboard (180x100 mm), cabos organizados, sensores e o suporte da bateria de 9V.

---

## Manual de Operação e Guia de Uso (IHM)

### 1. Liga / Desliga

- Pressione a tecla **`D`** no teclado matricial a qualquer momento para alternar entre **LIGADO** e **DESLIGADO**.
- Ao ligar, é apresentada a **logomarca temática em formato de uva** (criada através de caracteres customizados no LCD) durante 3 segundos.

### 2. Configuração Inicial (Data/Hora)

- Caso o RTC ainda não tenha sido configurado, o sistema abre automaticamente a tela de configuração ao ligar.
- Insira os valores utilizando as teclas numéricas (`0` a `9`):
  1. Dia (`DD`)
  2. Mês (`MM`)
  3. Ano (`AAAA`)
  4. Hora (`00-23`)
  5. Minuto (`00-59`)
- Utilize **`#`** para confirmar cada etapa e **`*`** para apagar/voltar.

### 3. Navegação pelos Menus

- **`A`**: Mover para cima no menu / Registro anterior.
- **`B`**: Mover para baixo no menu / Próximo registro.
- **`#`**: Selecionar / Entrar.
- **`*`**: Voltar / Cancelar.

### 4. Seleção de Região

- **América do Sul:** Unidade de temperatura em Celsius (°C), exibição de data no formato `DD/MM HH:MM`.
- **América do Norte:** Unidade de temperatura convertida para Fahrenheit (°F), exibição de data no formato `MM/DD HH:MM` (relógio de 12 horas).

### 5. Indicadores Visuais e Sonoros

- **Verde Contínuo:** Sistema ligado e operação em condições ideais.
- **Amarelo (Vermelho + Verde):** Leitura dos sensores em processamento / Modo de configuração.
- **Vermelho Piscando + Buzzer Intermitente:** Condição de alerta (parâmetros fora dos limites).
- **Apagado:** Sistema desligado ou em pausa entre leituras.

---

## Estrutura da Memória EEPROM

A memória EEPROM interna armazena as definições do usuário e um histórico circular de registros de alerta (até 100 entradas):

| Endereço na EEPROM | Variável / Conteúdo | Tamanho |
| :--- | :--- | :--- |
| `0` | Região Selecionada (0 = Sul, 1 = Norte) | 1 Byte |
| `1` | Total de Registros Guardados (`0` a `100`) | 1 Byte |
| `2` | Índice do Próximo Registro (Ponteiro FIFO) | 1 Byte |
| `3` a `702` | Bloco de Registros (7 Bytes por Registro) | 700 Bytes |
| `703` | Flag de Inicialização do RTC (`0xA5`) | 1 Byte |

Cada registro armazena: **Timestamp (4 Bytes)** + **Temperatura (1 Byte)** + **Umidade (1 Byte)** + **Luminosidade (1 Byte)**.

---

## Lista de Materiais (BOM)

- 1x Microcontrolador ATmega328P / Placa Arduino Uno R3
- 1x Sensor DHT22 (ou DHT11)
- 1x LDR (Fotorresistor) + Resistor de 10 kΩ
- 1x Módulo RTC DS1307 (I2C) + Bateria CR2032
- 1x Display LCD 16x2 com Módulo I2C (Endereço `0x27`)
- 1x Teclado Matricial 4x4
- 1x LED RGB Catodo Comum
- 1x Buzzer Piezoelétrico 5V
- 1x Bateria 9V + Conector com Chave
- Protoboard (180 mm x 100 mm), jumpers de ligação e resistores
- Gabinete impresso em 3D personalizado

---
/* ============================================================================
PROJETO: Data Logger Ambiental (Temperatura, Umidade e Luminosidade)
MCU: ATMEGA328P (Arduino Uno R3)
Sensores: DHT11 (temperatura/umidade) + LDR (luminosidade)
Periféricos: RTC DS1307, LCD 16x2 I2C, EEPROM interna,
teclado de membrana 4x4, LED RGB (status) e buzzer.
IDIOMA / REGIÃO
----------------
América do Sul ('A'): interface em PORTUGUÊS, temperatura em °C
América do Norte('B'): interface em INGLÊS, temperatura em °F
Os triggers continuam sempre em °C, então trocar de região muda o idioma
e a unidade exibida, mas nunca o significado do alerta.
FLUXO GERAL DA IHM
-------------------
1) Tela DESLIGADA (display apagado) até o usuário pressionar a tecla 'D'.
2) Ao pressionar 'D': liga o display, mostra a LOGO por alguns segundos.
3) Pede para o usuário informar a DATA/HORA atual (dia/mes/ano/hora/min).
4) Pede a REGIÃO: 'A' = América do Sul (°C), 'B' = América do Norte (°F).
5) MENU PRINCIPAL (lista navegável):
1 - Monitorar / Monitor
2 - Registros / View log
3 - Triggers / Triggers
4 - Limpar memoria / Clear memory
5 - Regiao / Region
TECLADO (navegação padronizada)
--------------------------------
A = sobe na lista B = desce na lista
# = confirma/seleciona * = volta
D = liga/desliga o display (0 a 9 digitam números na config. de data/hora)
LED RGB (cátodo comum) - ciclos do monitoramento
-------------------------------------------------
AMARELO = analisando (leitura dos sensores em andamento) - fica aceso
por ANALISE_MS para dar tempo de enxergar
VERDE = leitura concluída e tudo dentro da faixa - fica aceso pelo
tempo restante do ciclo (CICLO_MONITOR_MS - ANALISE_MS)
VERMELHO = alerta: pelo menos uma grandeza fora da faixa. Fica aceso
FIXO (sem piscar) durante todo o tempo em que o alerta durar.
ALERTA / BUZZER
----------------
O alerta dispara se QUALQUER uma das três grandezas estiver fora:
temperatura < 15°C ou > 25°C
umidade < 30% ou > 50%
luminosidade< 0% ou > 30%
Enquanto o alerta estiver ativo: o LED fica VERMELHO FIXO e o buzzer
apita CONTINUAMENTE.
A GRAVAÇÃO NA EEPROM ACONTECE SEMPRE, uma vez por minuto, nos estados
normal E alerta - o log guarda o histórico completo do ambiente.
MEMÓRIA / EEPROM
-----------------
Registro compacto (o mínimo possível):
timestamp relativo em MINUTOS desde a época do projeto: 2 bytes
temperatura: 1 byte - umidade: 1 byte - luminosidade: 1 byte
Total: 5 bytes por registro. Limite de 100 registros (buffer circular,
sobrescrevendo os mais antigos), ocupando 500 dos 1024 bytes da EEPROM.
============================================================================ /
#include <LiquidCrystal_I2C.h>
#include <RTClib.h>
#include <Wire.h>
#include <EEPROM.h>
#include "DHT.h"
// ============================== CONFIGURAÇÕES GERAIS =========================
#define SERIAL_OPTION 0 // 1 = liga prints de debug via Serial
// ----- Tempos do monitoramento (para os estados do LED ficarem visíveis) ----
const unsigned long ANALISE_MS = 1500; // amarelo aceso (analisando)
const unsigned long CICLO_MONITOR_MS = 5000; // ciclo total: 1,5s amarelo + 3,5s verde/vermelho
// ----- LCD 16x2 I2C -----
// Endereço: 0x27 ou 0x3F dependendo do módulo.
// Compartilha o barramento I2C do RTC (SDA->A4, SCL->A5), sem pino digital extra.
LiquidCrystal_I2C lcd(0x27, 16, 2);
// ----- RTC -----
RTC_DS1307 RTC;
// ----- DHT11 -----
#define DHTPIN 6
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);
// ----- LDR (luminosidade) - mesmo princípio do código do semáforo -----
#define LDRPIN A0 // referência do circuito original, usada para calibrar a leitura
// ----- LED RGB (catodo comum: HIGH = aceso) -----
#define RGB_RED_PIN 8
#define RGB_GREEN_PIN 9
#define RGB_BLUE_PIN 10 // combinando R+G igual a amarelo (não precisamos do azul puro)
// ----- Buzzer -----
#define BUZZER_PIN 12
// ----- Teclado de membrana 4x4 -----
// Linhas e colunas ligadas diretamente (sem biblioteca Keypad, para economizar memória)
// IMPORTANTE: A4 e A5 são reservados pelo barramento I2C (SDA/SCL) do RTC DS1307
// e NÃO podem ser usados por nenhum outro periférico (incluindo o teclado).
const int rowPins[4] = {A1, A2, A3, 7}; // L1..L4
const int colPins[4] = {2, 3, 4, 5}; // C1..C4
const char keyMap[4][4] = {
{'1','2','3','A'}, // A = navega para CIMA
{'4','5','6','B'}, // B = navega para BAIXO
{'7','8','9','C'},
{'','0','#','D'} // = voltar # = confirmar D = liga/desliga display
};
// ============================== EEPROM ========================================
// Registro: 2 bytes (minutos) + 1 (temp) + 1 (umid) + 1 (luz) = 5 bytes
const int recordSize = 5;
const int maxRecords = 100;
const int startAddress = 0;
int currentAddress = 0;
int recordCount = 0;
// Época de referência: 01/01/2024 00:00:00 (unixtime fixo)
const uint32_t PROJECT_EPOCH = 1704067200UL;
// ============================== REGIÃO / UNIDADES =============================
enum Region { REGION_SOUTH_AMERICA, REGION_NORTH_AMERICA };
Region currentRegion = REGION_SOUTH_AMERICA;
int utcOffsetSouthAmerica = -3; // Brasil
int utcOffsetNorthAmerica = -5; // EUA (Leste)
// ============================== TRIGGERS (sempre em °C e %) ==================
float trigger_t_min = 15.0; // ºC
float trigger_t_max = 25.0; // ºC
float trigger_u_min = 30.0; // %
float trigger_u_max = 50.0; // %
float trigger_l_min = 0.0; // %
float trigger_l_max = 30.0; // %
// ============================== ESTADO ========================================
enum AppState {
STATE_OFF, // display apagado, aguardando tecla D
STATE_SET_DATETIME, // configurando data/hora
STATE_SET_REGION, // escolhendo região
STATE_MENU, // menu principal
STATE_MONITOR, // tela de monitoramento ao vivo
STATE_VIEW_LOG, // visualização dos registros
STATE_SET_TRIGGERS, // ajuste de triggers
STATE_CLEAR_CONFIRM // confirmação de limpeza
};
AppState appState = STATE_OFF;
float currentTemp = 0; // sempre em °C internamente
float currentHumi = 0;
float currentLux = 0;
bool alertActive = false;
int lastLoggedMinute = -1;
int logViewIndex = 0;
int triggerFieldIndex = 0;
// Campos da configuração de data/hora
int dtStep = 0; // 0=dia,1=mes,2=ano,3=hora,4=min
int dtDay = 1, dtMonth = 1, dtYear = 2026, dtHour = 0, dtMinute = 0;
String dtInputBuffer = "";
// Controle do ciclo de monitoramento
unsigned long monitorCycleStart = 0;
bool monitorAmarelo = false;
bool monitorIniciado = false;
// ============================== LOGO (UVA MORDIDA) ============================
const char logo[16][21] = {
".....###..#.........",
".....####.#.........",
"....#####.#.........",
".....###..#.........",
"..........#.........",
"...##....##.........",
"..####..####..##....",
"..####..####.###....",
"...##....##..##.....",
"......##....##.##...",
".....####..####.....",
".....####..####.....",
"......##.##.##......",
"........####........",
"........####........",
".........##........."
};
byte caracteres[8][8];
// ============================== IDIOMA ========================================
// txt() escolhe o texto pelo idioma da região atual.
const char* txt(const char* pt, const char* en) {
return (currentRegion == REGION_NORTH_AMERICA) ? en : pt;
}
// ============================== SETUP =========================================
void setup() {
if (SERIAL_OPTION) Serial.begin(9600);
dht.begin();
Wire.begin();
RTC.begin();
if (!RTC.isrunning()) {
RTC.adjust(DateTime(F(__DATE__), F(__TIME__)));
}
// LED RGB e buzzer
pinMode(RGB_RED_PIN, OUTPUT);
pinMode(RGB_GREEN_PIN, OUTPUT);
pinMode(RGB_BLUE_PIN, OUTPUT);
pinMode(BUZZER_PIN, OUTPUT);
setRgb(false, false, false);
noTone(BUZZER_PIN);
// Teclado 4x4
for (int i = 0; i < 4; i++) {
pinMode(rowPins[i], OUTPUT);
digitalWrite(rowPins[i], HIGH);
}
for (int i = 0; i < 4; i++) {
pinMode(colPins[i], INPUT_PULLUP);
}
// LCD
lcd.begin(16, 2);
lcd.backlight();
criarCaracteres();
// Começa com o display apagado (requisito do projeto)
lcd.noBacklight();
lcd.noDisplay();
recordCount = countValidRecords();
currentAddress = (recordCount % maxRecords) * recordSize;
}
// ============================== LOOP PRINCIPAL ==================================
void loop() {
char key = readKeypad();
switch (appState) {
case STATE_OFF: handleStateOff(key); break;
case STATE_SET_DATETIME: handleStateSetDateTime(key); break;
case STATE_SET_REGION: handleStateSetRegion(key); break;
case STATE_MENU: handleStateMenu(key); break;
case STATE_MONITOR: handleStateMonitor(key); break;
case STATE_VIEW_LOG: handleStateViewLog(key); break;
case STATE_SET_TRIGGERS: handleStateSetTriggers(key); break;
case STATE_CLEAR_CONFIRM:handleStateClearConfirm(key); break;
}
}
// ============================== ESTADO: OFF =====================================
void handleStateOff(char key) {
if (key == 'D') {
lcd.backlight();
lcd.display();
mostrarLogo();
delay(3000);
lcd.clear();
startSetDateTime();
}
}
// ============================== CONFIGURAR DATA/HORA =============================
void startSetDateTime() {
appState = STATE_SET_DATETIME;
dtStep = 0;
dtInputBuffer = "";
DateTime now = RTC.now();
dtDay = now.day(); dtMonth = now.month(); dtYear = now.year();
dtHour = now.hour(); dtMinute = now.minute();
drawSetDateTimeScreen();
}
const char* dtStepLabel(int step) {
switch (step) {
case 0: return txt("Dia: ", "Day: ");
case 1: return txt("Mes: ", "Month: ");
case 2: return txt("Ano: ", "Year: ");
case 3: return txt("Hora: ", "Hour: ");
case 4: return txt("Minuto: ", "Minute: ");
}
return "";
}
void drawSetDateTimeScreen() {
lcd.clear();
lcd.setCursor(0, 0);
lcd.print(txt("Config. Data/Hr", "Set Date/Time"));
lcd.setCursor(0, 1);
lcd.print(dtStepLabel(dtStep));
}
void handleStateSetDateTime(char key) {
if (key == 0) return;
if (key >= '0' && key <= '9') {
if (dtInputBuffer.length() < 4) dtInputBuffer += key;
lcd.setCursor(0, 1);
lcd.print(dtStepLabel(dtStep));
lcd.print(" ");
lcd.print(dtInputBuffer);
lcd.print(" ");
} else if (key == '#') {
int value = dtInputBuffer.toInt();
applyDateTimeField(dtStep, value);
dtInputBuffer = "";
dtStep++;
if (dtStep > 4) {
RTC.adjust(DateTime(dtYear, dtMonth, dtDay, dtHour, dtMinute, 0));
startSetRegion();
} else {
drawSetDateTimeScreen();
}
} else if (key == '') {
if (dtInputBuffer.length() > 0) {
dtInputBuffer.remove(dtInputBuffer.length() - 1);
lcd.setCursor(0, 1);
lcd.print(dtStepLabel(dtStep));
lcd.print(" ");
lcd.print(dtInputBuffer);
lcd.print(" ");
}
}
}
void applyDateTimeField(int step, int value) {
switch (step) {
case 0: dtDay = value; if (dtDay < 1) dtDay = 1; if (dtDay > 31) dtDay = 31; break;
case 1: dtMonth = value; if (dtMonth < 1) dtMonth = 1; if (dtMonth > 12) dtMonth = 12; break;
case 2: dtYear = value; if (dtYear < 2000) dtYear = 2000; if (dtYear > 2099) dtYear = 2099; break;
case 3: dtHour = value; if (dtHour < 0) dtHour = 0; if (dtHour > 23) dtHour = 23; break;
case 4: dtMinute = value; if (dtMinute < 0) dtMinute = 0; if (dtMinute > 59) dtMinute = 59; break;
}
}
// ============================== ESCOLHER REGIÃO ==================================
void startSetRegion() {
appState = STATE_SET_REGION;
lcd.clear();
lcd.setCursor(0, 0);
lcd.print("A=South B=North");
lcd.setCursor(0, 1);
lcd.print(currentRegion == REGION_SOUTH_AMERICA ? "Regiao: Sul" : "Region: North");
}
void handleStateSetRegion(char key) {
if (key == 'A') {
currentRegion = REGION_SOUTH_AMERICA;
goToMenu();
} else if (key == 'B') {
currentRegion = REGION_NORTH_AMERICA;
goToMenu();
}
}
// ============================== MENU PRINCIPAL ==================================
const char* mainMenuItems[][2] = {
{"Monitorar", "Monitor"},
{"Registros", "View log"},
{"Triggers", "Triggers"},
{"Limpar memoria","Clear memory"},
{"Trocar regiao", "Change region"}
};
const int mainMenuItemsCount = 5;
int mainMenuIndex = 0;
void goToMenu() {
appState = STATE_MENU;
mainMenuIndex = 0;
stopMonitorOutputs();
drawMenuScreen();
}
void drawMenuScreen() {
lcd.clear();
lcd.setCursor(0, 0);
lcd.print("> ");
lcd.print(mainMenuItems[mainMenuIndex][currentRegion == REGION_NORTH_AMERICA ? 1 : 0]);
lcd.setCursor(0, 1);
lcd.print(mainMenuIndex + 1);
lcd.print("/");
lcd.print(mainMenuItemsCount);
lcd.print(" A^ Bv #ok vol");
}
void handleStateMenu(char key) {
switch (key) {
case 'A':
mainMenuIndex = (mainMenuIndex - 1 + mainMenuItemsCount) % mainMenuItemsCount;
drawMenuScreen();
break;
case 'B':
mainMenuIndex = (mainMenuIndex + 1) % mainMenuItemsCount;
drawMenuScreen();
break;
case '#':
switch (mainMenuIndex) {
case 0: startMonitor(); break;
case 1: logViewIndex = 0; appState = STATE_VIEW_LOG; drawViewLogScreen(); break;
case 2: triggerFieldIndex = 0; appState = STATE_SET_TRIGGERS; drawSetTriggersScreen(); break;
case 3: appState = STATE_CLEAR_CONFIRM; drawClearConfirmScreen(); break;
case 4: startSetRegion(); break;
}
break;
case '':
startSetDateTime();
break;
case 'D':
lcd.clear();
lcd.noBacklight();
lcd.noDisplay();
appState = STATE_OFF;
break;
}
}
// ============================== MONITORAMENTO AO VIVO ============================
void startMonitor() {
appState = STATE_MONITOR;
lcd.clear();
monitorIniciado = true;
monitorCycleStart = millis();
monitorAmarelo = true;
// Início do ciclo: AMARELO (analisando) por ANALISE_MS
setRgb(true, true, false);
}
void stopMonitorOutputs() {
monitorIniciado = false;
monitorAmarelo = false;
noTone(BUZZER_PIN);
setRgb(false, false, false);
}
void handleStateMonitor(char key) {
// Saída do monitoramento
if (key == '#') {
stopMonitorOutputs();
goToMenu();
return;
}
unsigned long agora = millis();
unsigned long decorrido = agora - monitorCycleStart;
// ---------- FASE AMARELA: analisando ----------
if (monitorAmarelo) {
if (decorrido < ANALISE_MS) {
// Continua amarelo FIXO (sem piscar) enquanto analisa
setRgb(true, true, false);
noTone(BUZZER_PIN);
return; // não decide cor final nem grava nada durante a análise
}
// Terminou o tempo de análise: faz a leitura e decide
readSensors();
evaluateAlert();
monitorAmarelo = false;
logToEEPROM(RTC.now());
// Leitura concluída: assume imediatamente a cor de estado
if (alertActive) {
setRgb(true, false, false); // vermelho fixo
tone(BUZZER_PIN, 1500); // buzzer contínuo enquanto durar o alerta
} else {
setRgb(false, true, false); // verde
noTone(BUZZER_PIN);
}
}
// ---------- FASE DE ESTADO: verde (normal) ou vermelho (alerta) ----------
drawMonitorScreen();
// ---------- GRAVAÇÃO NA EEPROM ----------
// Registra SEMPRE, uma vez por minuto, tanto em condição normal quanto
// em alerta - o log passa a guardar o histórico completo do ambiente.
DateTime now = RTC.now();
int offset = (currentRegion == REGION_SOUTH_AMERICA) ? utcOffsetSouthAmerica : utcOffsetNorthAmerica;
DateTime adjustedTime = DateTime(now.unixtime() + (uint32_t)(offset * 3600L));

// ---------- SAÍDA DE ESTADO: LED e buzzer ----------
if (alertActive) {
// ALERTA: vermelho FIXO e buzzer CONTÍNUO
setRgb(true, false, false);
tone(BUZZER_PIN, 1500);
} else {
// NORMAL: verde fixo e sem som
setRgb(false, true, false);
noTone(BUZZER_PIN);
}
// Fim do ciclo -> novo ciclo de análise (amarelo)
if (decorrido >= CICLO_MONITOR_MS) {
monitorCycleStart = agora;
monitorAmarelo = true;
setRgb(true, true, false); // volta ao amarelo para a próxima análise
}
}
void drawMonitorScreen() {
DateTime now = RTC.now();
int offset = (currentRegion == REGION_SOUTH_AMERICA) ? utcOffsetSouthAmerica : utcOffsetNorthAmerica;
DateTime adjustedTime = DateTime(now.unixtime() + (uint32_t)(offset * 3600L));
float dispTemp = currentTemp;
const char* tempUnit = "C";
if (currentRegion == REGION_NORTH_AMERICA) {
dispTemp = celsiusToFahrenheit(currentTemp);
tempUnit = "F";
}
// Linha 0: data/hora + estado (ANA/ALT em PT, SCN/ALR em EN)
lcd.setCursor(0, 0);
printTwoDigits(adjustedTime.day()); lcd.print("/");
printTwoDigits(adjustedTime.month()); lcd.print(" ");
printTwoDigits(adjustedTime.hour()); lcd.print(":");
printTwoDigits(adjustedTime.minute());
lcd.print(" ");
if (monitorAmarelo) lcd.print(txt("ANA", "SCN"));
else if (alertActive) lcd.print(txt("ALT", "ALR"));
else lcd.print("OK ");
lcd.print(" ");
// Linha 1: leituras (U de umidade em PT, H de humidity em EN)
lcd.setCursor(0, 1);
lcd.print("T");
lcd.print(dispTemp, 1);
lcd.print(tempUnit);
lcd.print(txt(" U", " H"));
lcd.print((int)currentHumi);
lcd.print("% L");
lcd.print((int)currentLux);
lcd.print("% ");
}
// ============================== VER REGISTROS ====================================
void handleStateViewLog(char key) {
int total = countValidRecords();
if (key == 'A' && total > 0) { logViewIndex = (logViewIndex - 1 + total) % total; drawViewLogScreen(); }
if (key == 'B' && total > 0) { logViewIndex = (logViewIndex + 1) % total; drawViewLogScreen(); }
if (key == '#' || key == '') goToMenu();
}
void drawViewLogScreen() {
int total = countValidRecords();
lcd.clear();
lcd.setCursor(0, 0);
if (total == 0) {
lcd.print(txt("Sem registros", "No records"));
lcd.setCursor(0, 1);
lcd.print(txt("#=voltar", "#=back"));
return;
}
uint16_t relMin; byte t, h, l;
readRecord(logViewIndex, relMin, t, h, l);
DateTime dt = DateTime(PROJECT_EPOCH + (uint32_t)relMin * 60UL);
lcd.print(txt("Reg ", "Rec "));
lcd.print(logViewIndex + 1);
lcd.print("/");
lcd.print(total);
lcd.print(" ");
printTwoDigits(dt.day()); lcd.print("/"); printTwoDigits(dt.month());
lcd.setCursor(0, 1);
float dispTemp = t;
const char* unit = "C";
if (currentRegion == REGION_NORTH_AMERICA) {
dispTemp = celsiusToFahrenheit((float)t);
unit = "F";
}
lcd.print("T");
lcd.print(dispTemp, 0);
lcd.print(unit);
lcd.print(txt(" U", " H"));
lcd.print(h);
lcd.print(" L");
lcd.print(l);
lcd.print(" Av B^ #vol");
}
// ============================== AJUSTAR TRIGGERS ==================================
const char* triggerLabel(int idx) {
switch (idx) {
case 0: return "T min (C)";
case 1: return "T max (C)";
case 2: return txt("U min (%)", "H min (%)");
case 3: return txt("U max (%)", "H max (%)");
case 4: return "L min (%)";
case 5: return "L max (%)";
}
return "";
}
float* triggerPointer(int idx) {
switch (idx) {
case 0: return &trigger_t_min;
case 1: return &trigger_t_max;
case 2: return &trigger_u_min;
case 3: return &trigger_u_max;
case 4: return &trigger_l_min;
case 5: return &trigger_l_max;
}
return &trigger_t_min;
}
void drawSetTriggersScreen() {
lcd.clear();
lcd.setCursor(0, 0);
lcd.print("Trig: ");
lcd.print(triggerLabel(triggerFieldIndex));
lcd.setCursor(0, 1);
lcd.print("Val:");
lcd.print(triggerPointer(triggerFieldIndex), 1);
lcd.print(" A+/B- #ok");
}
void handleStateSetTriggers(char key) {
float* ptr = triggerPointer(triggerFieldIndex);
if (key == 'A') { ptr += 1.0; drawSetTriggersScreen(); } // cima = incrementa
if (key == 'B') { ptr -= 1.0; drawSetTriggersScreen(); } // baixo = decrementa
if (key == '#') { // confirma e avança campo
triggerFieldIndex++;
if (triggerFieldIndex > 5) { triggerFieldIndex = 0; goToMenu(); }
else drawSetTriggersScreen();
}
if (key == '') { triggerFieldIndex = 0; goToMenu(); } // volta
}
// ============================== CONFIRMAR LIMPEZA ================================
void drawClearConfirmScreen() {
lcd.clear();
lcd.setCursor(0, 0);
lcd.print(txt("Limpar memoria?", "Clear memory?"));
lcd.setCursor(0, 1);
lcd.print(txt("A=sim B=nao", "A=yes B=no"));
}
void handleStateClearConfirm(char key) {
if (key == 'A') {
clearEEPROMLog();
goToMenu();
} else if (key == 'B') {
goToMenu();
}
}
// ============================== SENSORES =========================================
void readSensors() {
float h = dht.readHumidity();
float t = dht.readTemperature();
if (!isnan(h)) currentHumi = h;
if (!isnan(t)) currentTemp = t;
// Leitura do LDR (mesmo princípio do código do semáforo).
// Se no SEU circuito cobrir o LDR faz o valor SUBIR, inverta a escala:
// int lux = map(leituraLDR, 110, 350, 100, 0);
int leituraLDR = analogRead(LDRPIN);
int lux = map(leituraLDR, 110, 350, 0, 100);
if (lux < 0) lux = 0;
if (lux > 100) lux = 100;
currentLux = lux;
}
void evaluateAlert() {
alertActive = (currentTemp < trigger_t_min || currentTemp > trigger_t_max ||
currentHumi < trigger_u_min || currentHumi > trigger_u_max ||
currentLux < trigger_l_min || currentLux > trigger_l_max);
}
// ============================== CONVERSÃO DE UNIDADES =============================
float celsiusToFahrenheit(float c) {
return c * 9.0 / 5.0 + 32.0;
}
// ============================== LED RGB E BUZZER ==================================
void setRgb(bool red, bool green, bool blue) {
digitalWrite(RGB_RED_PIN, red ? HIGH : LOW);
digitalWrite(RGB_GREEN_PIN, green ? HIGH : LOW);
digitalWrite(RGB_BLUE_PIN, blue ? HIGH : LOW);
}
// ============================== EEPROM ===========================================
void logToEEPROM(DateTime now) {
uint32_t relSeconds = now.unixtime() - PROJECT_EPOCH;
uint16_t relMinutes = (uint16_t)(relSeconds / 60UL);
int tInt = (int)round(currentTemp);
int hInt = (int)round(currentHumi);
int lInt = (int)round(currentLux);
if (tInt < 0) tInt = 0;
if (tInt > 255) tInt = 255;
if (hInt < 0) hInt = 0;
if (hInt > 100) hInt = 100;
if (lInt < 0) lInt = 0;
if (lInt > 100) lInt = 100;
int address = startAddress + currentAddress;
EEPROM.put(address, relMinutes);
EEPROM.write(address + 2, (byte)tInt);
EEPROM.write(address + 3, (byte)hInt);
EEPROM.write(address + 4, (byte)lInt);
currentAddress += recordSize;
if (currentAddress >= (maxRecords * recordSize)) currentAddress = 0;
if (recordCount < maxRecords) recordCount++;
}
void clearEEPROMLog() {
for (int i = 0; i < maxRecords * recordSize; i++) {
EEPROM.write(startAddress + i, 0xFF);
}
currentAddress = 0;
recordCount = 0;
}
bool readRecord(int index, uint16_t &relMin, byte &t, byte &h, byte &l) {
if (index < 0 || index >= maxRecords) return false;
int address = startAddress + index * recordSize;
uint16_t storedMin;
EEPROM.get(address, storedMin);
if (storedMin == 0xFFFF) return false;
relMin = storedMin;
t = EEPROM.read(address + 2);
h = EEPROM.read(address + 3);
l = EEPROM.read(address + 4);
return true;
}
int countValidRecords() {
int count = 0;
uint16_t relMin; byte t, h, l;
for (int i = 0; i < maxRecords; i++) {
if (readRecord(i, relMin, t, h, l)) count++;
}
return count;
}
// ============================== TECLADO DE MEMBRANA 4x4 ==========================
char readKeypad() {
for (int r = 0; r < 4; r++) {
digitalWrite(rowPins[r], LOW);
for (int c = 0; c < 4; c++) {
if (digitalRead(colPins[c]) == LOW) {
delay(20); // debounce simples
if (digitalRead(colPins[c]) == LOW) {
while (digitalRead(colPins[c]) == LOW); // espera soltar a tecla
digitalWrite(rowPins[r], HIGH);
return keyMap[r][c];
}
}
}
digitalWrite(rowPins[r], HIGH);
}
return 0; // nenhuma tecla pressionada
}
// ============================== LOGO (LCD custom chars) ==========================
void criarCaracteres() {
for (byte blocoLinha = 0; blocoLinha < 2; blocoLinha++) {
for (byte blocoColuna = 0; blocoColuna < 4; blocoColuna++) {
byte indice = blocoLinha * 4 + blocoColuna;
for (byte linha = 0; linha < 8; linha++) {
byte valor = 0;
for (byte coluna = 0; coluna < 5; coluna++) {
valor <<= 1;
if (logo[blocoLinha * 8 + linha][blocoColuna * 5 + coluna] == '#') {
valor |= 1;
}
}
caracteres[indice][linha] = valor;
}
lcd.createChar(indice, caracteres[indice]);
}
}
}
void mostrarLogo() {
lcd.clear();
const byte inicio = 6;
lcd.setCursor(inicio, 0);
lcd.write(byte(0));
lcd.write(byte(1));
lcd.write(byte(2));
lcd.write(byte(3));
lcd.setCursor(inicio, 1);
lcd.write(byte(4));
lcd.write(byte(5));
lcd.write(byte(6));
lcd.write(byte(7));
}
// ============================== AUXILIARES =======================================
void printTwoDigits(int value) {
if (value < 10) lcd.print("0");
lcd.print(value);
}

---

## Como Compilar e Carregar

O código deve ser compilado utilizando a IDE do Arduino e carregado no Arduino Uno / ATmega328P.

---

## Links e Entregáveis

Os arquivos relacionados ao projeto podem ser disponibilizados junto ao código-fonte e à documentação do produto.
