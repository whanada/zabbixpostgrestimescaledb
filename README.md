#Variaveis utilizadas em todos os composes
#criar .env posteriormente

<DB_USER> - Usuario do banco
<DB_PASSWORD> - Senha do usuario do banco
<NETWORK> - rede criada no Docker

#Inicie por deploy do banco

docker stack deploy zabbix -c database.yml

#Deploy do Servidor em HA em dois nós

docker stack deploy zabbix -c ha_server.yml

#Deploy do Front

docker stack deploy zabbix -c front.yml

#Assim que subir a aplicação, precisa escalar ela para zero para aplicar o timescaleDB no banco de dados. Nao tem como aplicar timescale sem a aplicação ter criado o banco pela primeira vez.

docker service scale zabbix_server-ha1=0 zabbix_server-ha2=0

#verifique que todos containers estão rodando:

docker ps

#entre dentro do container

docker exec -it "$(docker ps | grep timescale | awk '{print $1}')" bash

#entre dentro do banco

psql -U <DB_USER> -d zabbix

Executar o script do arquivo timescaledb.sql

#Subir o servidor novamente

docker service scale zabbix_server-ha1=1 zabbix_server-ha2=1
