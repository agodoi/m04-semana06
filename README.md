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
   - Primeiramente, você precisa criar uma conta no Ubidots e iniciar um projeto. Durante esse processo, você obtém um token de acesso exclusivo associado ao seu projeto. Para esse caso do Inteli, a conta será criada pela coordenação/instrutor de programação.

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


