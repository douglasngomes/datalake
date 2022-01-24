# datalake
Camada de Mensagem - Apache Kafka


O Apache Kafka é uma plataforma open-source para processamento de streaming de dados, escrita em Scala e Java. Uma plataforma unificada de alta produtividade e baixa latência, para tratamento de feeds de Dados em ‘real time’ ou ‘near real time’.
Sua camada de armazenamento é essencialmente uma ‘Fila de mensagens massivamente escalável, arquitetada como um log de transações distribuídas’. 
O Apache Kafka é considerado um MOM – middleware orientado a mensagens, que suporta o envio e recebimento de mensagens entre sistemas distribuídos.
O Apache Kafka também pode ser usado como uma ferramenta de ETL, e funciona muito bem com o SPARK, que é uma solução excelente para Processamento de Dados.


•	ARQUITETURA DO APACHE KAFKA

O broker faz o controle entre o que entra (Producers) e o destino (Consumers), o Kafka funciona como o middleware, um tipo de caixa de correio que recebe a mensagem e disponibiliza para o consumidor.
O broker é o executável que vai interagir com o sistema operacional onde os dados serão gravados. 
O broker pode ser distribuído em mais de uma máquina, ou podemos ainda ter dois brokers na mesma máquina, ou vários brokers em várias máquinas, tudo vai depender do objetivo do projeto.
A gestão do Cluster Kafka precisa ser feita em outro serviço, o Apache Zookeeper para sistemas distribuídos. O Apache Zookeeper é um serviço centralizado para manter metadados sobre o cluster de nodes distribuídos, ou seja, gerenciar um sistema distribuído.





•	INSTALAÇÃO DO APACHA KAFKA

Vamos instalar o Apache Kafka no nosso hdpmaster (servidor virtual na nuvem – EC2 AWS).


*Abra o terminal e inicialize o servidor hadoop

ssh  -i “xxxx-xxxx.pem” hadoop@ec2-xx-xxx-xx-xxx.us-east-2.compute.amazonaws.com

sudo yum update + (senha)


*Faça o download – site Kafka.apache.org (versão binary downloads: scala 2.11 – kafka-2.11.1.1.0.tgz)

cd /opt/

sudo wget (cópia do link do download)


*Descompacte o arquivo: 

sudo tar -xvf kafka-2.11.1.1.0.tgz


*Mova o arquivo para diretório kafka: 

sudo mv kafka-2.11.1.1.0 kafka


*Remova o aquivo tgz do diretório: 

sudo rm -rf  kafka-2.11.1.1.0.tgz

ls -la


*Mude para proprietário do diretório kafka: 

sudo chown -R hadoop:hadoop kafka/


*Configure as variáveis de ambiente:,

cd~

vi .bash_profile

Digite ( i ) para entrar no modo de inserção.

#KAFKA

Export KAFKA_HOME=/opt/kafka

Export PATH=$PATH:$KAFKA_HOME/bin

Digite esc, :w (salvar), :wq (salvar e sair)

source .bash_profile


*Finalizamos aqui a instalação do Kafka, vamos verificar o diretório

cd /opt/kafka

ls -la

Ps: O Apache Zookeeper já esta disponível no diretório com a instalação do Apache Kafka, esta no diretório libs.





•	APACHE KAFKA TOPICS

É considerado a abstração central do Kafka. É uma categoria de mensagens, podendo existir vários topics dentro de um mesmo broker, ou até mesmo um topic em mais de um broker. Fisicamente é um arquivo de log.

•	APACHE KAFKA MESSAGE

Em toda message teremos um ‘timestamp’ para controle de tempo e versionamento, outros elementos da message são o ‘id único’ e os ‘dados’ (conteúdo).
O serviço off-set vai controlar as mensagens que já foram lidas pelo consumer, o off-set é uma variável, um placeholder que vai guardar a posição da última mensagem lida, dentro de uma ordenação, é o serviço Consumer o encarregado de manter o off-set.
Ps: O kafka retém todas as mensagens publicadas independente do consumo, por um período padrão de 168 horas (07 dias). Pode ser configurado outro período, específico por topic. Lembre-se de considerar sempre as restrições de espaço em disco.

Produzindo e Consumindo Mensagens com Apache KAFKA

Configurando e iniciando um kafka cluster com apache zookeeper. Utilizaremos apenas o hdpmaster (node master aws EC2).

1 - Abra o terminal e inicialize o servidor hadoop

ssh  -i “xxxx-xxxx.pem” hadoop@ec2-xx-xxx-xx-xxx.us-east-2.compute.amazonaws.com


*Inicialize o zookeeper:

cd /opt/kafka

bin/zookeeper-server-start.sh   config/zookeeper.properties


*Teste para confirmar se o serviço esta no ar: 

sudo yum install telnet

telnet localhost 2222

stat


Ps: Após o teste remova o telnet, pois não é muito seguro, isso é uma boa prática de segurança: 


*Agora inicialize o broker kafka: 

cd /opt/kafka

bin/kafka-server-start.sh   config/server.properties

Ps: A configuração padrão do kafka vai trazer apenas um broker

vi server.properties

:wq (salvar e sair)


2 – Criando um Apache Kafka Topic

cd /opt/kafka

bin/kafka-topics.sh  --create  --topic  topico1  --zookeeper  localhost:2222  --replication-factor 1  --partitions 1


*Listando o topic: 

bin/kafka-topics.sh  --list  --zookeeper  localhost:2222

Ps: A conexão com o broker foi feita usando o zookeeper como gerenciador, é ele que envia a conexão. Vamos agora criar o nosso Producer e o Consumer.


3 – Produzindo Mensagens para o Topic (Producer)

bin/kafka-console-producer.sh  --broker-list  localhost:3333  --topic  topico1

>Mensagem 1
>
>Mensagem 2
>
>Mensagem 3


4 – Consumindo as Mensagens no Topic (Consumer)

bin/kafka-console-consumer.sh  --zookeeper  localhost:2222  --topic  topico1  --from-beginning



Ps: A conexão é na porta do zookeeper, posso ter topics em vários brokers, e é o zookeeper que vai gerenciar essa coleta. O que fiz foi informar o nome do topic e para fazer a coleta à partir do início.
Com a conexão ativa o que entrar de Mensagem no Producer vai ser conduzido para o Consumer via broker e com o gerenciamento do zookeeper.



