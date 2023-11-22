# Comunicação à Distância

Nesse encontro, veremos como a comunicação entre o microcontrolador e os blocos externos acontece, como por exemplo, como o ESP32 utiliza a comunicação WiFi com o protocolo MQTT passando pelas camadas de Rede, Internet, Transporte e Aplicação. A parte prática será o controle de um LED por meio da Internet e Ubidots.

## Arquitetura do projeto usando Ubidots

Confira na figura a seguir, como será o seu projeto usando a plataforma Ubidots.

O Ubidots é uma plataforma IoT que oferece uma variedade de recursos para facilitar o desenvolvimento e gerenciamento de projetos IoT. Vatagens:

1. **Interface Amigável:** O Ubidots possui uma interface de usuário intuitiva e amigável, o que facilita a configuração, monitoramento e análise de dados do seu dispositivo IoT. Não precisa desenvolver seu front-end nesse projeto.

2. **Suporte a Diversos Dispositivos e Protocolos:** A plataforma suporta uma variedade de dispositivos e alguns protocolos IoT (MQTT e HTTP), permitindo a integração fácil de diferentes tipos de sensores e hardware. Em especial, vamos usar o protocolo MQTT e a quantidade de sensor que o ESP32 suportar.

3. **Visualização de Dados em Tempo Real:** Fornece ferramentas para visualização de dados em tempo real, incluindo gráficos e dashboards personalizáveis, permitindo que os usuários monitorem facilmente o status de seus dispositivos IoT.

4. **Regras e Ações Personalizadas:** Permite a configuração de regras e ações personalizadas com base nos dados coletados, facilitando a automação e a resposta a eventos específicos.

5. **Segurança:** Oferece recursos de segurança robustos para proteger os dados transmitidos e armazenados, garantindo a integridade e confidencialidade das informações.

6. **Escalabilidade:** A plataforma é projetada para ser escalável, permitindo que os usuários aumentem o número de dispositivos conectados e gerenciem grandes frotas de dispositivos IoT.

7. **Facilidade de Conectividade:** Simplifica a configuração de conexões entre dispositivos e a nuvem, proporcionando uma experiência mais suave para os desenvolvedores.

8. **Suporte Técnico:** Oferece suporte técnico para ajudar os desenvolvedores a superar desafios e resolver problemas durante a implementação de projetos IoT.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m04-semana06/blob/main/imgs/arquitetura_geral_mqtt.jpg">
   <img alt="Família ESP32" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m04-semana06/blob/main/imgs/arquitetura_geral_mqtt.jpg)">
</picture>

O Ubidots é uma plataforma IoT baseada em nuvem que permite a coleta, armazenamento, visualização e análise de dados provenientes de dispositivos IoT. O processo de como os dados chegam ao Ubidots, são validados por meio de token e processados pelas informações do dispositivo IoT geralmente segue os seguintes passos:

## Documentação Ubidots

[Referência](https://docs.ubidots.com/reference/welcome)

[Códigos](https://github.com/ubidots)

[Exemplos de Desenvolvimento](https://dev.ubidots.com/devices/industrial-iot?_gl=1*dmqooy*_ga*NDExMzY1MTYxLjE2OTQ1NDI2MTA.*_ga_VEME7QQ5EZ*MTcwMDY1NzMwNy4xNi4xLjE3MDA2NTc1MDguNjAuMC4w)

[Exemplo de Projetos](https://www.hackster.io/ubidots/projects)

## Diagrama de Blocos do Funcionamento

1. **Criação de uma Conta e Projeto:**
   - Primeiramente, você precisa criar uma conta no Ubidots e iniciar um projeto. Durante esse processo, você obtém um token de acesso exclusivo associado ao seu projeto. Para esse caso do Inteli, você terá que abrir a sua conta pessoal Ubidots e ainda, terá outra conta que será criada pela coordenação/instrutor de programação. Essa segunda conta será a oficial do seu projeto. Hoje vamos criar a sua conta pessoal, e depois, num segundo momento, chegará a segunda conta. Essa segunda conta estará no nome do representante do grupo, mas a senha será pública entre as pessoas do mesmo grupo.

2. **Registro do Dispositivo:**
   - Para cada dispositivo IoT que você deseja conectar ao Ubidots, você precisará registrá-lo no seu projeto com um label. Isso é feito no seu código-fonte do ESP32 e o Ubidots criará automaticamente caso ele nãop esteja criado. Veja o diagrama de blocos a seguir para melhor entender.

3. **Integração do Dispositivo com a API Ubidots:**
   - No firmware ou software do seu dispositivo IoT, você incorporará a lógica para enviar dados para o Ubidots usando a API fornecida pela plataforma. Isso geralmente é feito por meio de solicitações HTTP ou MQTT, dependendo do protocolo que você escolheu no seu projeto. Nesse projeto, vamos focar no MQTT.

4. **Envio de Dados para o Ubidots:**
   - Periodicamente, o dispositivo envia dados para o Ubidots que ele chama de **DOT**. Esses DOT inclui **valeu**, **timestamp** e **context**. Veja a figura a seguir:

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m04-semana06/blob/main/imgs/dc0ffa6-ubidots-data.png">
   <img alt="Família ESP32" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m04-semana06/blob/main/imgs/dc0ffa6-ubidots-data.png)">
</picture>


#### Valores [value]
Um valor numérico. O Ubidots aceita número de ponto flutuante de até 16 casas decimais.

{"valor" : 34.87654974}

#### Carimbos de Tempo [timestamp]

Um carimbo de tempo é uma maneira de rastrear o tempo como um total contínuo de segundos. Esta contagem começou no Unix Epoch em 1º de janeiro de 1970, no UTC. Portanto, o carimbo de tempo Unix é meramente o número de segundos entre uma data específica e o Unix Epoch. Por favor, tenha em mente que ao enviar dados para o Ubidots, você deve definir o carimbo de tempo em milissegundos. Além disso, se você recuperar o carimbo de tempo de um ponto, este estará em milissegundos.

"carimbo de tempo" : 1537453824000

O carimbo de tempo acima corresponde a quinta-feira, 20 de setembro de 2018, às 14:30:24.

DICA PROFISSIONAL: Uma ferramenta útil para converter entre carimbos de tempo Unix e datas legíveis por humanos é o Epoch Converter.

#### Contexto [context]

Valores numéricos não são o único tipo de dado suportado. Você também pode armazenar tipos de dados de string ou char no que chamamos de contexto. O contexto é um objeto de chave-valor que permite armazenar não apenas valores numéricos, mas também valores de string. Um exemplo de uso do contexto poderia ser:

"contexto" : {"status" : "ligado", "clima" : "ensolarado"}

O contexto é comumente usado para armazenar as coordenadas de latitude e longitude do seu dispositivo para casos de uso de GPS/rastreamento. Todos os mapas do Ubidots usam as chaves lat e lng do contexto de um ponto para extrair as coordenadas do seu dispositivo. Dessa forma, você só precisa enviar um único ponto com os valores das coordenadas no contexto da variável para plotar um mapa, em vez de enviar separadamente tanto a latitude quanto a longitude em duas variáveis diferentes. Abaixo, você pode encontrar um contexto típico com valores de coordenadas:

"contexto" : {"lat":-6.2, "lng":75.4, "clima" : "ensolarado"}

Observe que você pode misturar tanto valores de string quanto numéricos no contexto. Se sua aplicação for para fins de geo-localização, certifique-se de que as coordenadas estejam definidas em graus decimais.

5. **Validação do Token:**
   - Cada solicitação de dados enviada pelo dispositivo inclui o token de acesso associado ao seu projeto Ubidots. Esse token é validado pela plataforma para garantir que a solicitação seja legítima e autorizada.

6. **Armazenamento dos Dados:**
   - Após a validação do token, os dados enviados pelo dispositivo são armazenados nos servidores do Ubidots. Eles podem ser organizados em variáveis específicas, que representam diferentes tipos de dados ou informações do dispositivo.

7. **Processamento e Visualização dos Dados:**
   - Os dados armazenados podem ser processados e visualizados por meio do painel de controle do Ubidots. Os desenvolvedores podem criar dashboards personalizados para monitorar em tempo real o desempenho de seus dispositivos IoT.

8. **Configuração de Regras e Ações (Opcional):**
   - O Ubidots oferece recursos avançados, como a configuração de regras e ações automáticas com base nos dados recebidos. Isso permite automatizar respostas a determinados eventos, como alertas ou acionamento de dispositivos.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m04-semana06/blob/main/imgs/diagrama_blocos.png">
   <img alt="Família ESP32" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m04-semana06/blob/main/imgs/diagrama_blocos.png)">
</picture>

## Parte prática

Etapas:

1) Abra sua conta particular e gratuita do [Ubidots Stem](https://ubidots.com/stem). Fique atento que o final da URL tem que ser STEM para ser uma conta de estudante;
2) Instale a biblioteca Ubidots no Arduino IDE. Puxe os dois arquivos zip [dessa pasta](https://drive.google.com/drive/folders/10FC273CVdW05TD02czb3Ic7egROspJ67?usp=share_link) e adicione na sua IDE;
3) Faça o [donwload](https://docs.google.com/document/d/16XECwRzPNouLuc8NXMfkkcSZ2op2C6hjdl63yghBveo/edit?usp=sharing) desse exemplo de Liga-Desliga LED e salve no seu computador;
4) Deixe o seu ESP32 próximo do seu protoboard (ou até afixe seu ESP32 no próprio protoboard) para conectar a um LED externo;
5) Coloque um resistor de aproximadamente 330 ohms no pino positivo do LED, e no pino negativo do LED, conecte ao GND do ESP32;
6) Ligue o outro terminal do R no pino D2 do ESP32;
7) Atualize esses campos do código-fonte da sua forma:
```
const char *UBIDOTS_TOKEN = "";  // peque o seu TOKEN da sua conta particular. Vá na sua foto do seu perfil do Ubidots, e clique em "Credenciais da API" e pegue o "Default Token" e cole aqui
const char *WIFI_SSID = "";      // use o WiFi do seu celular nesse primeiro momento, pois o proxy local pode bloquear seu acesso
const char *WIFI_PASS = "";      // use a senha senha do roteador do seu celular
const char *DEVICE_LABEL = "";   // crie um nome qualquer para o seu dispositivo
const char *VARIABLE_LABEL = ""; // crie um nome qualquer de variável que terá os dados da sua aplicação

```
8) Grave esse código no seu ESP32;
9) Vá no seu ambiente Ubidots, e clique em **Dispositivos** e novamente, selecione a opção **Dispositivos**;
10) Você já verá o label do device que você criou em ```const char *DEVICE_LABEL = ""```;
