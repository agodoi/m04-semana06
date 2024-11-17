# Sistema de Disparo de Atuadores

Neste encontro, será apresentado como ativar acionadores básicos (linha de força, motores, relés) a partir da resposta de sensores de forma a implementar regras de atuação. Adicionalmente, trataremos no detalhe, como funciona a comunicação I2C.

## Assuntos relacionados

- Atuadores

- Interfaces de comunicação

- Microcontroladores

- HiveMQ

## Perguntas que podem impactar o seu projeto:

### (1) Quantos sensores I2C cabem no ESP32?

### (2) Quais ideias podem melhorar o seu projeto?

### (3) Qual a vantagem de se usar sensores I2C?

### (4) Quais os cuidados se você usar relés no seu projeto?

### (5) Vantagens do HiveMQ vs Ubidots

https://portal.vidadesilicio.com.br/descobrindo-o-endereco-i2c/


## Protocolo I2C

O barramento I2C (Inter-Integrated Circuit) é um protocolo de comunicação serial que permite a interconexão de vários dispositivos em um mesmo barramento. 

No ESP32, o I2C é implementado por meio de hardware e software. Veja na Fig. 1, como se comporta fisicamente as conexões eletrônica no barramento I2C.


<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m04-semana08/blob/main/imgs/barramentoI2C.png">
   <img alt="Barramento I2C" src="(https://github.com/agodoi/m04-semana08/blob/main/imgs/barramentoI2C.png)" width="700px">
</picture>


Note que o mesmo par de fios SDA e SCL é usado para conectar todos os sensores. Não pode haver inversão desses pinos. Se isso acontecer, toda a comunicação será interrompida. A função de cada pino é a seguinte:

- **SDA (Serial Data Line):** Pino para a linha de dados. Este pino é usado para enviar e receber dados.
- **SCL (Serial Clock Line):** Pino para a linha de clock. Este pino é usado para sincronizar a transferência de dados entre dispositivos.

Na figura a seguir, é possível entender melhor como um projeto prático pode ser montado:

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m04-semana08/blob/main/imgs/projetoPraticoI2C.png">
   <img alt="Circuito I2C" src="(https://github.com/agodoi/m04-semana08/blob/main/imgs/projetoPraticoI2C.png) width="100px">
</picture>

Veja que o ESP32 já possui os pinos SDA e SCL prontos para serem utilizados. Evite nomear outros pinos como SDA e SCL, pois podem não funcionar adequadamente.


<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m04-semana08/blob/main/imgs/ESP32-Pinout.jpg">
   <img alt="Pinout" src="(https://github.com/agodoi/m04-semana08/blob/main/imgs/ESP32-Pinout.jpg) width="500px">
</picture>


## Quantidade de Dispositivos Suportados

O barramento de endereçamento I2C possui 7 bits. Isso significa que a quantidade teórica de dispositivos suportados é de ```N = 2^7 = 128```. A faixa de endereços é dada na base hexadecimal. Portanto, você poderia (teoricamente) usar números hexadecimais de **00** até **7F**. Contudo, alguns endereços são reservados e fixos, e por isso, a quantidade prática de dispositivos suportados é de até 112. Contudo, você terá um outro ponto de atenção: fonte de alimentação adequada para alimentar tantos sensores.

E ainda, alguns dispositivos I2C já possuem um endereço fixo entre 00 e 7F. Essa fixação é feita pelo fabricante por meio de microjumpers soldados na placa. Então, ao invés de tentar encontrar na sorte de qual endereço tal dispositivo está fixado, use esse código para você varrer os 128 endereços teóricos. Esse código-fonte pergunta ao dispositivo, qual o endereço ele está respondendo. Note que a rotina de varredura conta de 0 a 127, mas o resultado impresso no Monitor Serial da IDE do Arduino é convertido para hexadecimal. Você pode usar endereços de barramento tanto em decimal quanto em hexadecimal.

```
#include <Wire.h>
 
void setup() {
  Wire.begin();
  Serial.begin(115200);
  Serial.println("\nScanner I2C");
    byte error, address;
  int nDevices;
  Serial.println("Varrendo...");
  nDevices = 0;
  for(address = 1; address < 127; address++ ) {
    Wire.beginTransmission(address);
    error = Wire.endTransmission();
    if (error == 0) {
      Serial.print("Dispositivo I2C encontrado no endereço 0x");
      if (address<16) {
        Serial.print("0");
      }
      Serial.println(address,HEX);
      nDevices++;
    }
    else if (error==4) {
      Serial.print("Erro desconhecido no endereço 0x");
      if (address<16) {
        Serial.print("0");
      }
      Serial.println(address,HEX);
    }    
  }
  if (nDevices == 0) {
    Serial.println("Nenhum dispositivo I2C encontrado\n");
  }
  else {
    Serial.println("pronto\n");
  }
  delay(5000);  
  Serial.print("Dispositivos I2C encontrados:");
  Serial.println(nDevices); 
}
 
void loop() {
      
}
```

## Comunicação I2C:

A comunicação I2C é iniciada pelo mestre (ESP32) e pode envolver um ou mais dispositivos escravos. A comunicação consiste em transferências de dados em bytes. O mestre envia um endereço de dispositivo seguido por dados ou recebe dados do dispositivo escravo. **Você pode usar endereços na base decimal (0 até 127) ou hexadecimal (x00 até x7F)**.

A frequência padrão de transferência de dados é de 100kHz, mas pode chegar a 5MHz.

A vantagem de usar a comunicação I2C é facilidade e simplicidade da pinagem. O código-fonte é relativamente simples para extrair dados dos sensores.

## Cabeçalho do I2C

A figura a seguir mostra como o cabeçalho do I2C é organizado. Note que possui os 7 bits do endereçamento e na sequência, 8 bits de dados. É nesses campo de dados que os valores do sensor são transferidos ao ESP32.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m04-semana08/blob/main/imgs/I2C_Basic_Address_and_Data_Frames.jpg">
   <img alt="Cabeçalho I2C" src="(https://github.com/agodoi/m04-semana08/blob/main/imgs/I2C_Basic_Address_and_Data_Frames.jpg)">
</picture>

## Documentação Oficial I2C

Esse link está a documentação oficial da fabricante do ESP32, chamada Espressif.

[Documentação](https://espressif-docs.readthedocs-hosted.com/projects/arduino-esp32/en/latest/api/i2c.html)

### Configuração do Barramento I2C:

Para usar o I2C no ESP32, é necessário inicializar e configurar o barramento. A biblioteca mais usada para o I2C chama-se **Wire.h**.

```
#include <Wire.h>

void setup() {
  Wire.begin();          // Inicializa o barramento I2C
  Serial.begin(115200);  // Inicializa a comunicação UART, que nesse caso, ocorre pelo cabo USB.
}

void loop() {
  // Seu código aqui
}
```

#### Enviando dados para um dispositivo escravo:

```
#include <Wire.h>

void setup() {
  Wire.begin();
  Serial.begin(115200);
}

void loop() {
  Wire.beginTransmission(8); // 8 é o endereço do dispositivo escravo
  Wire.write(42);            // Dado a ser enviado
  Wire.endTransmission();
  delay(500);
}
```

#### Recebendo dados de um dispositivo escravo:

```
#include <Wire.h>

void setup() {
  Wire.begin();
  Serial.begin(115200);
}

void loop() {
  Wire.requestFrom(8, 6); // Solicita 6 bytes do dispositivo escravo com endereço 8
  while (Wire.available()) {
    char c = Wire.read(); // Lê os dados recebidos
    Serial.print(c);
  }
  delay(500);
}
```

## Ideias de aprimoramento

O aprimoramento vem com a adição de novas informações ao seu projeto. Tente adicionar sensores que lhe tragam mais insights:

- De barreira por infravermelho que conta acessos de pessoas ou registro de pessoas no local

- Piezoelétrico que converte vibração mecânica em sinal elétrico

- De presença por infravermelho que registra pessoas de uma forma mais ampla

- De toque em maçanetas ou portas usando entradas touch nativas do ESP32

 Locais que podem inserir sensores:

- Tapetes
  
- Sola de sapatos

- Roupas

- Batentes de portas

- Acessórios e ardonos

- Relógios

- Capinhas de celular

- Medidor de nível do óleo

- No interruptor da lâmpada do local

- Botão de comando da prensa

## O que é um relé?

Um relé é um dispositivo eletromecânico que utiliza um eletroímã para controlar um ou mais contatos elétricos. Sua principal função é abrir ou fechar circuitos elétricos, permitindo o controle de dispositivos de alta potência por meio de sinais elétricos de baixa potência. Vamos explorar o funcionamento de um relé em detalhes:

### Componentes de um Relé:

1. **Eletroímã (Bobina):**
   - O coração do relé é a bobina, uma espira de fio condutor. Quando uma corrente elétrica passa pela bobina, cria um campo magnético em torno dela.

2. **Contatos (Comutadores):**
   - Os relés têm contatos móveis que podem estar em uma de duas posições: normalmente abertos (NA) ou normalmente fechados (NF). Os contatos normalmente abertos estão abertos quando a bobina não está energizada, e os contatos normalmente fechados estão fechados quando a bobina não está energizada.

3. **Mola de Retorno:**
   - Para garantir a comutação rápida dos contatos, um relé geralmente possui uma mola de retorno que retorna os contatos à sua posição original quando a bobina é desenergizada.

4. **Armatura (Mecanismo de Comutação):**
   - A armatura é uma peça móvel que é atraída ou repelida pelo campo magnético gerado pela bobina. A armatura é conectada aos contatos móveis.

### Funcionamento Básico:

1. **Desenergizado (Repouso):**
   - Quando a bobina não está energizada, a armatura é mantida em sua posição original pela mola de retorno. Nessa situação, os contatos podem ser normalmente abertos (NA) ou normalmente fechados (NF) dependendo do tipo de relé.

2. **Energizado:**
   - Quando uma corrente elétrica é aplicada à bobina, ela gera um campo magnético que atrai a armatura. A armatura é puxada para a bobina contra a força da mola de retorno.

3. **Comutação dos Contatos:**
   - A movimentação da armatura causa a comutação dos contatos. Se os contatos estavam normalmente abertos, agora eles se fecham; se estavam normalmente fechados, agora eles se abrem.

4. **Desenergização:**
   - Quando a corrente é removida da bobina, a mola de retorno retorna a armatura à sua posição original. Os contatos retornam à sua condição normal (normalmente abertos ou normalmente fechados).

### Diagrama Esquemático:

Aqui está um diagrama esquemático simplificado para ajudar a visualizar o funcionamento:

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m04-semana08/blob/main/imgs/diagrama_rele.png">
   <img alt="Diagrama Rele" src="(https://github.com/agodoi/m04-semana08/blob/main/imgs/diagrama_rele.png) width="100px">
</picture>

1. **Bobina (Circuito de Controle):**
   - O símbolo da bobina representa o circuito de controle que é conectado à fonte de alimentação.

2. **Contatos (Circuito Controlado):**
   - Os contatos NA (normalmente abertos) e NF (normalmente fechados) representam os circuitos controlados pelo relé.

### Imagem do Interior de um Relé:

[Interior de um Relé](https://en.m.wikipedia.org/wiki/File:Relay_principle_horizontal_new.gif)

1. **Bobina (Parte Superior):**
   - A bobina está localizada na parte superior e é representada pela espira de fio.

2. **Armatura (Peça Móvel):**
   - A armatura é a peça móvel que se move quando a bobina é energizada.

3. **Contatos (Parte Inferior):**
   - Os contatos móveis estão na parte inferior e são comutados pela armatura.

Este é um resumo geral do funcionamento de um relé. A complexidade pode variar dependendo do tipo específico de relé (por exemplo, relé eletromagnético, relé de estado sólido), mas os princípios básicos permanecem os mesmos.

Os campos vetoriais são uma ferramenta matemática usada para descrever o comportamento de quantidades vetoriais em diferentes pontos do espaço. Embora o funcionamento específico de um relé não seja geralmente descrito usando campos vetoriais, podemos fazer uma analogia simplificada para ajudar a entender como a energia eletromagnética age sobre a armatura do relé.

## Como energizar um rele?

Para energnizar um relé, simples use o comando ```digitalWrite(pinoRele,LOW)``` ou ```digitalWrite(pinoRele,HIGH)```. O mais comum é o ```LOW```.

Veja um exemplo:

```
// Define os pinos utilizados
const int botaoPin = 2;  // Pino digital conectado ao botão
const int relePin = 3;   // Pino digital conectado ao relé

void setup() {
  // Configuração dos pinos
  pinMode(botaoPin, INPUT_PULLUP); // Configura o pino do botão como entrada com pull-up interno
  pinMode(relePin, OUTPUT);         // Configura o pino do relé como saída
  digitalWrite(relePin, HIGH;      // Inicializa o relé como desativado
}

void loop() {
  // Verifica se o botão foi pressionado
  if (digitalRead(botaoPin) == LOW) {
    // Se o botão foi pressionado, ativa o relé
    digitalWrite(relePin, LOW);
    delay(500); // Adiciona um atraso para evitar ativação repetida imediata (debounce)
    
    // Aguarda até que o botão seja liberado antes de desativar o relé
    while (digitalRead(botaoPin) == HIGH) {
      delay(10); // Debounce
    }
    
    // Desativa o relé
    digitalWrite(relePin, HIGH);
  }
}
```

### Análise do Funcionamento do Relé com Conceitos Matemáticos:

1. **Campo Magnético (B):**
   - A corrente elétrica que flui através da bobina do relé gera um campo magnético (B). Podemos considerar o campo magnético como um vetor que possui magnitude e direção em cada ponto ao redor da bobina.

2. **Armatura como Vetor Posição (r):**
   - Podemos associar a armatura do relé com um vetor posição (r), representando a posição da armatura no espaço. À medida que a armatura se move, podemos considerar sua posição em relação à bobina.

3. **Força Magnética (F):**
   - A armatura, que atua como uma carga magnética, experimenta uma força (F) devido à interação com o campo magnético. A relação entre o campo magnético, a corrente e a força é descrita pela Lei de Ampère.

4. **Equação Vetorial:**
   - Podemos expressar essa interação em termos de uma equação vetorial, onde a força magnética (F) é proporcional ao produto vetorial da corrente (I) e do vetor posição (r), de acordo com a regra da mão direita.

5. **Movimento da Armatura:**
   - Quando a corrente é aplicada à bobina, o campo magnético resultante exerce uma força sobre a armatura, movendo-a na direção determinada pelo vetor campo magnético.

6. **Comutação dos Contatos:**
   - O movimento da armatura causa a comutação dos contatos. Se considerarmos a posição da armatura como um parâmetro de controle, podemos relacionar seu movimento aos estados dos contatos (abertos ou fechados).

Essa abordagem é uma simplificação e não representa um modelo matemático rigoroso, mas pode ajudar a visualizar o funcionamento do relé em termos de campos vetoriais. Em termos práticos, os engenheiros elétricos geralmente usam equações específicas para descrever o comportamento eletromagnético, e os campos vetoriais são ferramentas poderosas para análise em sistemas mais complexos.


## Quais cuidados ao usar relé?

O relé um dispositivo que usa o campo magnético formado em sua bobina interna para atrair uma pequena chapinha de metal.

Ao utilizar relés em projetos com o ESP32, é importante ter alguns cuidados para garantir o bom funcionamento, a segurança e a confiabilidade do sistema. Aqui estão alguns cuidados a serem considerados:

1. **Proteção contra Sobrecarga e Transientes:**
   - Utilize diodos em paralelo aos contatos do relé para proteger contra picos de tensão gerados pela desenergização do relé. Isso ajuda a prevenir danos aos pinos de saída do ESP32.

2. **Corrente e Tensão Nominal:**
   - Verifique se a corrente e a tensão nominais do relé estão dentro das especificações suportadas pelo ESP32 e pela fonte de alimentação.

3. **Alimentação Isolada:**
   - Considere alimentar o relé com uma fonte de alimentação isolada para evitar interferências elétricas que possam afetar a estabilidade do ESP32.

4. **Capacidade do Pino de Saída:**
   - Certifique-se de que os pinos de saída do ESP32 possuem capacidade de corrente suficiente para acionar o relé. Se necessário, utilize um driver de transistores para aumentar a corrente disponível.

5. **Proteção Eletromagnética (EMI/RFI):**
   - Adote práticas de design para minimizar interferências eletromagnéticas que podem afetar a operação do ESP32.

6. **Proteção contra Curto-Circuito:**
   - Inclua fusíveis ou PTCs (coeficiente de temperatura positivo) para proteger o circuito contra curtos-circuitos no lado do relé.

7. **Proteção Térmica:**
   - Considere a dissipação de calor do relé e adote medidas para evitar o superaquecimento. Isso pode incluir dissipadores de calor ou escolha de relés de estado sólido que geram menos calor.

8. **Evite Correntes Elevadas no Controle:**
   - Evite correntes excessivas nos pinos de controle do relé. Utilize resistores de base ou optoacopladores para isolar o circuito de controle do relé do circuito de controle do ESP32.

9. **Proteção contra Reversão de Polaridade:**
    - Implemente proteção contra inversão de polaridade na fonte de alimentação do sistema para evitar danos aos componentes.

10. **Testes e Monitoramento:**
    - Realize testes extensivos para garantir a confiabilidade do sistema. Monitore o comportamento do sistema em diferentes condições de carga.
