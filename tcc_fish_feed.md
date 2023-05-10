# metodologia de funcionamento

A ideia é montar um alimentador de peixes que dado um determinado valor de pH da água, horario ideal e temperatura, o sistema irá conferir se há ração suficiente e se todas as condições forem verdadeiras a alimentação será feita.

Para monitoramento e conferencia dos valores e funções será utilizado o aplicativo Blynk na versão free.

### Materiais utilizados

| Sensor/Modulo | Modelo | Imagem |
| --- | --- | --- |
| Sensor de Temperatura | DS18B20 | <img src="https://cdn.awsli.com.br/600x700/248/248356/produto/11923698/b84e008472.jpg" width="150"> |
| Sensor Ultrasom | HC-SR04 | <img src="https://m.media-amazon.com/images/I/41xyYRYlEUL._AC_.jpg" width="150"> |
| Sensor pH | PH-4502C | <img src="https://cdn.awsli.com.br/600x450/2492/2492563/produto/1780373964f38a39a96.jpg" width="150"> |
| Placa Arduino | MEGA 2560 | <img src="https://w7.pngwing.com/pngs/100/516/png-transparent-arduino-mega-2560-micro-usb-graphics-cards-video-adapters-usb-electronics-electronic-device-electrical-connector.png" width="150"> |
| Módulo Relay | 4-Channel 5V | <img src="https://cdn.shopify.com/s/files/1/1978/9859/products/02_122_large.jpg?v=1502520103" width="150"> |
| Enthernet Shield | HR911105A | <img src="https://http2.mlstatic.com/D_NQ_NP_639610-MLB49085489702_022022-O.jpg" width="150"> |
| Fonte de alimentação Arduino | 9v | <img src="https://www.makerhero.com/wp-content/uploads/2017/07/Fonte-DC-Chaveada-9V-1A-Plug-P4.jpg" width="150"> |
| Motor DC 12V | MMAMUTE  | <img src="https://balanaagulha.com.br/wp-content/uploads/2022/01/50.png" width="150"> |
| Fonte de alimentação Motor DC | HT121810A | <img src="https://m.media-amazon.com/images/I/71bbkiLgZ-L._AC_UF1000,1000_QL80_.jpg" width="150"> |

## Bibliotecas utilizadas

| Nome | Link |
| --- | --- |
| ``<SPI.h>`` | https://github.com/arduino/ArduinoCore-avr/tree/master/libraries/SPI |  
| ``<Ethernet.h>`` | https://github.com/arduino-libraries/Ethernet |
| ```<BlynkSimpleEthernet.h>``` | https://github.com/blynkkk/blynk-library/blob/master/src/BlynkSimpleEthernet.h |
| ``<TimeLib.h>`` | https://github.com/PaulStoffregen/Time/blob/master/TimeLib.h |  
| ``<HCSR04.h>`` | https://github.com/gamegine/HCSR04-ultrasonic-sensor-lib |
| ```<OneWire.h>``` | https://www.arduinolibraries.info/libraries/one-wire |
| ```<DallasTemperature.h>``` | https://www.arduinolibraries.info/libraries/dallas-temperature |


## Container e sensor ultrasom
O container consiste em um galão de 20 litros que tem aproximadamente 35 cm de profundidade dada a posição do sensor. No funcionamento normal o sensor de ultrasom devolve a distancia do objeto a sua frente. Através do uso da regra de 3 e formulas matemáticas foi possível criar um gráfico que representa o nível de ração do container no aplicativo.
Além disso, foram criadas regras que mudam a cor do gráfico proporcionamente ao nível de ração do container.


| Porcentagem | Cor |
| --- | --- |
| Maior que 75% | <span style="color:green">Verde<span> |  
| Entre 75% e 20% | <span style="color:orange">Laranja<span> |
| Menor que 20% | <span style="color:red">Vermelho<span> |

>O sensor realiza uma nova medida a cada 10 segundos.

Uma peculiaridade sobre o sensor de ultrasom foi que ao jogar ele no loop não funcionava corretamente porque as outras funções impediam a leitura constante dele. Para resolver este problema foi necessário a utilização de uma biblioteca chamada ``<HCSR04.h>`` Essa biblioteca dispobibiliza uma função que gera a distancia em centimentros não sendo necessário calcular na mão.

Primeiro é necessário inicializar o sensor

```c
UltraSonicDistanceSensor distanceSensor(trigPin, echoPin);
```
Após isso basta chamar a seguinte função

```c
distanceCm = distanceSensor.measureDistanceCm();
```

>Outro detalhe importante sobre esse sensor é que a função deve ser chamada apenas uma vez, se for usada duas vezes seguidas os valores podem variar ou vir errados.

## Sensor de temperatura
O sensor de temperatura DS18B20 utilizado neste trabalho além de funcionar bem é a prova d'água. Ele será responsável por medir a temperatura da água apenas. No aplicativo além de exibir os valores da temperatura, o valor também pode mudar de cor conforme o nível de temperatura da seguinte forma:


| Temperatura | Cor |
| --- | --- |
| Entre 25º e 28º | <span style="color:green">Verde<span> |  
| Menor que 25º | <span style="color:#2ECCFA">Azul<span> |
| Maior que 28º | <span style="color:red">Vermelho<span> |

>O sensor realiza uma nova medida a cada 10 segundos.

## Data e hora
Quando um novo projeto é criado no aplicativo blynk o mesmo pede para que seja definida a localização para ajuste de data e hora (_timezone_). Quando o aplicativo inicia a conexão a primeira coisa que ele faz é a sincronização de data e hora, portanto o relógio está sempre correto. Isso dispensa o uso do módulo RTC.

>Data e hora são atualizados a cada 10 segundos.

## Relay e motor alimentador

O relay pode ser considerado a parte mais simples do projeto, pois a função dele é apenas ativar ou desativar o aliemntador. O que vai definir a quantidade de ração é o tempo em que o motor ficará em atividade

> o relay só será ativado se a função de alimentar for chamada. Para isso, todos os parâmetros devem estar adequados.

## funcionamento do alimentador
