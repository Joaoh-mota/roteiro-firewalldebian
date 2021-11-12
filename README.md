# roteiro criação do firewall no debian

apt-get update

apt-install iptables

iptables -L para listar as regras existentes

iptables -I para adicionar regra no inicio da lista

iptables -A adiciona a regra no fim da lista

iptables -D apaga uma regra

iptables -F apaga todas as regras

## criando uma regra que bloqueia o serviço de ping

placa de rede em modo bridge para que haja comunicação com a maquina externa

faça o ping no debian com o windows, após certificar que existe comunicação partiremos para o bloqueio

o ping usa o protocolo ICMP

Para criar a regra de bloqueio do ping no inicio da lista basta digitar:

iptables -I INPUT -p ICMP -j DROP

esta regra será uma regra de input que irá dropar o pacote, bloqueando o ping no servidor sem dar aviso ao emissor

se quiser que o emissor receba um aviso, digite a regra:

iptables -I INPUT -p ICMP -j REJECT 

para remove-las basta digitar iptables -F

porem ao inserir uma regra e reiniciar o servidor, ele perderá todas as regras do firewall
dessa forma faz-se necessário criar um script de execução

## Criando script do firewall

va para cd /usr/local/bin

crie o script do firewall nesse diretório

vi firewall.sh

no arquivo criado digite:

#!/bin/bash

iptables -I INPUT -p ICMP -j DROP

Salve e saia do arquivo

execute agora o comando chmod para mudar as permissões do arquivo

chmod +x firewall.sh

após criar o script, execute-o com sh firewall.sh

quando executar digite iptables -L e a regra estará criada

porém ao reiniciar a máquina a regra continuára sendo deletada

por isso execute vá até o diretório system pois nele é armazenado todos os scripts que serão executados ao iniciar a máquina

cd /etc/systemd/system/

agora crie o arquivo que será responsável por iniciar o serviço quando ligar o sistema

vi firewall.service

[Unit]

Description=Firewall

[Service]

ExecStart=/usr/local/bin/firewall.sh start

ExecStop=/usr/local/bin/firewall.sh stop

ExecRestart=/usr/local/bin/firewall.sh restart


[Install]

WantedBy=multi-user.target

salve e saia

execute o comando systemctl daemon-reload para atualizar o systemd

agora execute systemctl enable firewall depois systemctl start firewall

e agora digite systemctl status firewall para verificar que o serviço está ativo e iniciando junto com o servidor
