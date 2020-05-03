#drbd-heartbeat
Nodo01
IP: 192.168.100.128
IP: 10.10.20.1
SO: Debian 9

Nodo02
IP: 192.168.100.129/24
IP: 10.10.20.2

Copiar o arquivo /etc/drbd.conf
- Preparando o disco rígido
- Para o comando abaixo funcionar o recurso deve está declarado corretamente
        on nodo01 # O nome aqui deve está o mesmo em "uname -a" {
                device /dev/drbd0;
                disk /dev/sdb1;
                address 10.10.10.1:7788;
                meta-disk internal;
        }
        on nodo02 {
                device /dev/drbd0;
                disk /dev/sdb1;
                address 10.10.10.2:7789;
                meta-disk internal;
        }
}
# modprobe cn
# modprobe drbd
# depmod -a 
# drbdadm md-create /dev/drbd0
# Após isso é só seguir 

– Reinicie o serviço do DRBD nos dois servidores:
# /etc/init.d/drbd restart
 
– No Servidor principal, vamos começar a sincronizar os discos:
# drbdadm -- --overwrite-data-of-peer primary all
 
É muito importante essa primeira sincronia. Por isso veja o andamento da sincronia com o comando abaixo:
# watch -n1 cat /proc/drbd
 
9 – Depois de terminado a sincronia, formate o disco virtual no primario:
# mkfs.ext4 /dev/drbd0
 
10 – Agora vamos montar o disco:
# mount /dev/drbd0 /dados
 
obs: “dados” é a pasta que você deve criar nos dois servidores para serem o ponto de montagem do disco virtual.

No segundo servidor não precisa formatar a partição.

# Diferentemente do tutorial do Douglas..
Nâo precisei dos comandos abaixo:
# drbdadm connect r1
# drbadm attach r1

Outros comandos:
# drbdadm primary all
# drbdadm secondary all
