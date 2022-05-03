# Sobre o kafka - https://kafka.apache.org/
- Plataforma de streaming distribuida (é além de um sistema de fila como o rabbitmq)
- É um banco de dados, armazena os dados (não faz os dados 100% em memória)
- Você pode configurar o período de exclusão das mensagens no kafka
- É super rápido e tem baixa latência
- Armazena os dados de forma distribuída entre os seus clusters elencando automaticamente as partições principais em cada cluster
- Processa mensagens em tempo real
- Tem por padrão o serviço de pub/sub através dos groupsId
- Ele não garante a ordem das mensagens (ordem é somente dentro de uma partição, porém em um sistema distribuido, ele recupera todas as mensagens de todas as partições, não garantindo a ordem)
- Por padrão você consegue enviar mensagens com o tamanho limite de 1MB, esta mudança pode ser feita no arquivo: server.properties em: message.max.bytes=20971520

## Install
- https://kafka.apache.org/downloads
- https://www.tutorialkart.com/apache-kafka/install-apache-kafka-on-mac/
```shell
nano ~/.bash_profile
# colocar no bash_profile:
alias "start_zookeeper= sh ~/kafka_2.13-3.1.0/bin/zookeeper-server-start.sh ~/kafka_2.13-3.1.0/config/zookeeper.properties" 
alias "start_kafka= sh ~/kafka_2.13-3.1.0/bin/kafka-server-start.sh ~/kafka_2.13-3.1.0/config/server.properties" 
source ~/.bash_profile
```

## Tópicos
- Grupo de mensagens armazenadas para que um consumidor possa ler

## Zookeeper
- Sistema gerenciador das mensagens em partições por tópicos no Kafka

## Cluster Kafica
- Um conjunto de maquinas distribuindo e dando performance a um kafka com balanciamento

## Brokers
- Servidores que são utilizados para armazenar/entrega as mensagens
- Ele armazena as mensagens em partições diferentes em cada Broker, deixando uma partição em cada Broker diferente

## Partitions
- Número de filas que armazena as mensagens de um mesmo tópico, as mensagens são distribuidas entre as filas automaticamente pelo Kafka, para fazer essa distribuição ele leva em consideração a key que o producer esta informando quando cria a mensagem. Para distribuir as mensagens para mais de um consomer, temos que ter mais de uma partitions. O número de partitions deve ser definida no arquivo de confinuração, quando o kafka é startado, e também deve estar definida no topico, caso o topico já esteja criado pode ser editado.
```shell
# alterar as particoes de um topico
./bin/kafka-topics.sh --alter --bootstrap-server localhost:9092 --partitions 3 --topic PRIMEIRO_TOPICO

# mostrar as particoes de um topico
./bin/kafka-topics.sh --describe --bootstrap-server localhost:9092 --topic PRIMEIRO_TOPICO
```

## Replication Factor
- Define quantas replicas das partições você irá querer em cada Broker
- Se deixarmos somente uma replica, caso perca um dos clusters, todas as mensagens daquele cluster serão perdidas
- Quem faz este gerencimento é o Zookeeper

## Offset
- Posição da mensagem armazenada em uma partição

## Producer
- Sistema que envia as mensagens

## Consumer
- Sistema que consome as mensagens

<hr>

# enviando e consumindo mensagem via terminal
```shell
# lista topicos
~/kafka_2.13-3.1.0/bin/kafka-topics.sh --list --bootstrap-server localhost:9092

# cria um topico
~/kafka_2.13-3.1.0/bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic PRIMEIRO_TOPICO

# enviando mensagem
~/kafka_2.13-3.1.0/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic PRIMEIRO_TOPICO 
# irá abrir um console para você digitar vários valores, exemplo:
# > valor
# > valor

# consumindo mensagems
~/kafka_2.13-3.1.0/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic PRIMEIRO_TOPICO  # consome somente as mesagens novas
~/kafka_2.13-3.1.0/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic PRIMEIRO_TOPICO  --from-beginning # lê mensagens deste o inicio

# deletar mensagem de um tópico
echo '{ "partitions": [ { "topic": "PRIMEIRO_TOPICO", "partition": 0, "offset": 1 } ],  "version": 1 }' > delete-records.json
~/kafka_2.13-3.1.0/bin/kafka-delete-records.sh --bootstrap-server=localhost:9092 --offset-json-file delete-records.json

# deletar um topico
~/kafka_2.13-3.1.0/bin/kafka-topics.sh --topic EXEMPLO_TOPICO  --delete --bootstrap-server localhost:9092

```

# para criar um balanciamento de consumidores alterar o arquivo server.properties
```shell
vim ~/kafka_2.13-3.1.0/config/server.properties 
# alterar o valor: 
# num.partitions=3

# agora é alterar o tópico
~/kafka_2.13-3.1.0/bin/kafka-topics.sh --alter --bootstrap-server localhost:9092 --topic TOPICO_1 --partitions 3

# para verificar as partições de um tópido
~/kafka_2.13-3.1.0/bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe

# agora 2 consumidores passam a trabalhar em paralelo
# para testar, rodar 2 consumidores:
# ./start.sh # terminal 1
# ./start.sh # terminal 2
```


