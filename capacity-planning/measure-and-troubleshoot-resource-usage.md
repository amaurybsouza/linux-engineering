
- comando padrão do linux ao invés do ifconfig -> ip address show
- vamos instalar dois pacotes que são usados nesta aula.


$ yum install net-tools syssatat -y


# VAMOS COMEÇAR PELOS COMANDOS INICIAIS:


#iostat (mostra um relatório de uso de CPU e de IO dos dispositivos de armazenamento, de CPU)

# whatis iostat 
iostat (1)           - Report Central Processing Unit (CPU) statistics and input/output statistics for devices and partitions.

- sempre ver o man, whatis para ajudar com os comandos do Linux.




-> Principais opções do comando iostat:

#iostat 

Linux 5.4.0-31-generic (dell-inspiron) 	27/05/2020 	_x86_64_	(4 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
          36,26    0,15   11,83    0,04    0,00   51,72

Device             tps    kB_read/s    kB_wrtn/s    kB_dscd/s    kB_read    kB_wrtn    kB_dscd
loop0             0,00         0,02         0,00         0,00       1090          0          0
loop1             0,10         0,12         0,00         0,00       6029          0          0
loop10            0,00         0,02         0,00         0,00       1071          0          0
loop11            0,00         0,02         0,00         0,00       1079          0          0
loop12            0,00         0,00         0,00         0,00          1          0          0
loop2             0,00         0,01         0,00         0,00        344          0          0
loop3             0,02         0,03         0,00         0,00       1478          0          0
loop4             0,09         0,10         0,00         0,00       4745          0          0
loop5             0,00         0,02         0,00         0,00       1083          0          0
loop6             0,20         0,20         0,00         0,00       9811          0          0
loop7             0,00         0,01         0,00         0,00        341          0          0
loop8             0,00         0,01         0,00         0,00        404          0          0
loop9             1,44         1,46         0,00         0,00      70714          0          0
sda              12,70        61,87       224,82         0,00    3005109   10919609          0

- entendendo as opções desse comando: (observar esses dados em problemas de performance do sistema)

1) %user (é o tempo de CPU de programas a nivel de usuário, root, meu usuário)

2) %nice (aplicações a nivel de usuário que foi definido alguma prioridade especifica)

3) %system (uso de CPU pelo kernel, praticamente o kernel)

4) %iowait (quanto tempo a CPU ficou esperando algum procedimento de entrada, escrita em disco)
   - por exemplo, isso é relevante se você ver que o disco está lento, lento para ler ou escrever algum setor do disco
   - ou alguma aplicaçao tendo dificuldade em escrver algo no disco, isso causa tempo de CPU elevando, %iowait

5) %steal (usado em máquina virtuais, CPU virtual, quanto tempo a CPU da maquina virtual esperou a CPU da maquina
real disponibilzar recurso para ela).

6) %idle (tempo que a CPPU ficou em estado idle)

- outro dado importante desse comando, são os devices (dispositivos de armazenamento da máquina)

tps (transações em segundos, média)

7) opções do comando iostat:

- $iostat -m (mostra os dados em megas)
- $iostat -h (mostra os dados mais humanos, mais legivel)
- iostat -c (mostra só os dados de CPU)
- iostat -d (mostra só os devices)
- iostat -p (mostra as partições)

- iostat -c 2 (gera um relatório a cada dois segundos)
- iostat -d 2 (gera um relatório a cada dois segundos)
- iostat -c 2 5 (relatório a cada 2 segundos e em 5 vezes)
 


#vmstat (alternativa ao comando iostat, mostra informações sobre processos, memória, paginação, blocos de IO)

# vmstat 
procs -----------memória--------- ---swap-- ----e/s---- -sistema- ------cpu-----
 r  b   swpd  livre   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 5  0      0 1340280 493688 6325104    0    0    35   124  400  133 35 12 53  0  0

- informações importante desse comando:

r (o que está na fila do processamento dos processadores)
b (estado de bloqueio, blocked, sleep)

- vmstat -d (dispositivos da máquina)
- vmstat -p /dev/sda8 (mostra os dados de uma partição especifica)

#mpstat (mostra informações sobre CPU, mais detalhado que no comando iostat)


- mpstat 2 3 (mostra a cada 2 segundos por 3 vezes as informações)


#sar (mostra o histórico de uso dos recursos de hardware do sistema)

- Em sistemas Debian preciso ativar a flag para que o comando sar seja ativado.

# vim /etc/default/sysstat

# systemctl status sysstat.service 
● sysstat.service - Resets System Activity Data Collector
     Loaded: loaded (/lib/systemd/system/sysstat.service; enabled; vendor preset: enabled)
     Active: active (exited) since Thu 2020-05-28 07:59:47 -03; 1h 41min ago
       Docs: man:sa1(8)
             man:sadc(8)
             man:sar(1)
    Process: 770 ExecStart=/usr/lib/sysstat/debian-sa1 --boot (code=exited, status=0/SUCCESS)
   Main PID: 770 (code=exited, status=0/SUCCESS)

mai 28 07:59:47 dell-inspiron systemd[1]: Starting Resets System Activity Data Collector...
mai 28 07:59:47 dell-inspiron systemd[1]: Finished Resets System Activity Data Collector.

- existe um arquivo que /etc/cron.d/ que faz essa coleta dos dados, de nome sysstat.

$ whatis sadc
sadc (8)             - System activity data collector.

- onde ele salva os dados coletados:

$ /var/log/sysstat

- opções do comando sar:

$ sar -u (dados de todas as CPU do sistema)
$ sar -f sa10 (mostra os dados do dia 10/05, histórico desse dia)
$ sar -r (mostra dados de memória do sistema) ** parecido com o comando free 
$ sar -S (mostra dados de swap do sistema) 

swap free, swap usada
$ sar -n DEV (mostra dados referentes a interface de redes)
sar -n DEV
Linux 3.10.0-1127.8.2.el7.x86_64 (centos1) 	28-05-2020 	_x86_64_	(1 CPU)

09:37:36          LINUX RESTART

09:40:01        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
09:50:01       enp0s3      0,27      0,40      0,05      0,04      0,00      0,00      0,16
09:50:01       enp0s8      0,54      0,56      0,05      0,08      0,00      0,00      0,16
09:50:01           lo      0,00      0,00      0,00      0,00      0,00      0,00      0,00
Média:        enp0s3      0,27      0,40      0,05      0,04      0,00      0,00      0,16
Média:        enp0s8      0,54      0,56      0,05      0,08      0,00      0,00      0,16
Média:            lo      0,00      0,00      0,00      0,00      0,00      0,00      0,00

$ sar -d (mostra escrita em disco, interfaces de disco)

$ sar -r -f sa08 (mostra o consumo de memória do dia 8)

$ sar -u 2 (mostra dados de cpu a cada 2 segundos, opção bem utilizada)

$ sar -u 2 5 (a cada 2 segundos por 5 vezes)

$ sar -n DEV (mostra dados das interfaces de redem RX, TX trafego de rede nas interfaces)

- vale mencionar que o cron coleta e armazena a cada 10 minutos nos logs (var/log/sysstat)
- analise de performance do sistema.


#netstat (mostra por padrão as conexões de redes, tabelas de roteamento, muita informação)

- algumas opções do comando netstat:



$ netstat -i (interfaces de rede da maquina e as estatisticas de redes) ** mesmo que o comando ifconfig

# netstat -i
Tabela de Interfaces do Kernel
Iface             MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
enp0s3           1500      670      0      0 0           992      0      0      0 BMRU
enp0s8           1500     1207      0      0 0          1414      0      0      0 BMRU
lo              65536       32      0      0 0            32      0      0      0 LRU

$ netstat -s (mostra as estatisticas por protocolo, resumo de tragefo de cada protocolo)
# netstat -s
Ip:
    806 total packets received
    0 forwarded
    0 incoming packets discarded
    609 incoming packets delivered
    481 requests sent out
    8 outgoing packets dropped
    56 dropped because of missing route
Icmp:
    17 ICMP messages received
    0 input ICMP message failed.
    Histograma de entrada ICMP:
        destination unreachable: 16
        echo requests: 1
    17 ICMP messages sent
    0 ICMP messages failed
    Histograma de saída ICMP
        destination unreachable: 16
        echo replies: 1

$ netstat -a (mostra todos os sockets, processo, um arquivo que está em execução)

#ss (novo comando que vem substituindo o netstat, visa substituir)

- o netstat pega as informações lá do proc/sys (reflexo do que existe no kernel)
- o ss acessa a memória do kernel e pega as informações, mais confiavel, direto na fonte

- opções do comando ss:

$ ss (todas as conexões, tcp, sockets, fechadas)
$ ss -s (mostra o resumo dos pacotes, tcp, udp)
# ss -s
Total: 195 (kernel 240)
TCP:   7 (estab 1, closed 1, orphaned 0, synrecv 0, timewait 0/0), ports 0

Transport Total     IP        IPv6
*	  240       -         -        
RAW	  2         0         2        
UDP	  3         2         1        
TCP	  6         3         3        
INET	  11        5         6        
FRAG	  0         0         0 

$ ss -l (conexões em modo listen)
$ ss -t (conexões tcp)
$ ss -tnl (conexões tcp em listen, aguardando conexão)


#iptraf (monitora o trafego em tempo real, o ambiente)


## COMANDOS RECURSOS ATRAVÉS DE PROCESSOS


#ps axu | less 

VSZ (toda memória que o processo pode acessar).

RSS (toda memória RAM que está alocada para tal processo)

- outro comando importante

$w (quem está conectado na máquina e que processo está rodando)
# w
 10:48:01 up  1:10,  1 user,  load average: 0,00, 0,01, 0,03
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    10.0.0.6         09:38    1.00s  0.16s  0.01s w


$lsof (identifica processo com arquivos, usado em sockets)


- vamos fazer um teste com esse comando.

1) vou criar um arquivo do zero:

2) vou procurar o processo desse arquivo:
$ps axu | grep  vi | grep teste

3) achar o processo com lsof
$lsof -p 18578

4) agora vou procurar com o lsof o nome do arquivo que estou editando:
$lsof /home/amaury/.teste.txt.swp
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1000/gvfs
      Output information may be incomplete.
lsof: WARNING: can't stat() fuse file system /run/user/1000/doc
      Output information may be incomplete.
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
vim     18578 amaury    3u   REG    8,5    12288 9458689 /home/amaury/.teste.txt.swp

- esse comando mostra quem criou  o arquivo.

- agora vendo as minhas conexões com SSH:

# lsof -i :22
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd      859 root    3u  IPv4  28236      0t0  TCP *:ssh (LISTEN)
sshd      859 root    4u  IPv6  28247      0t0  TCP *:ssh (LISTEN)
ssh     16414 root    3u  IPv4 175203      0t0  TCP 10.0.0.6:40956->10.0.0.8:ssh (ESTABLISHED)

-> link importante: https://stackoverflow.com/questions/7880784/what-is-rss-and-vsz-in-linux-memory-management



#top  (importante lembrar a média de cargo do sistema)

load average: 0,00, 0,01, 0,03 (média dos ultimos 1 minutos, 5 minutos, 15 minutos)

- para ordenar os campos do comando top, usando as teclas (shift + f)


#htop (não é instalado por padrão, é um top mais melhorado, ele mostra o consumo por cpu)

$ htop + l (mostra os arquivos abertos)

$ htop -p 29202 (mostra os detalhes desse processo em especifico, telegra, obs)

$ htop -d 100 (equivale a 10 segundos)

$ htop -d 1000 (equivale a 100 segundos)


##############

200.1 – Medir e Resolver Problemas de Uso de Recursos - Swap, uptime e free


# uptime (mostra o load average, informação mais importante desse comando, é a linha inicial do top)

- são processos que ficaram aguardando execução, para serem executados.

- se tem 4 cpus o ideal é que fique até 4, se tiver mais, pode ser que esteja criando filas.


# free -h (area de swap, é para ser complementar a memória RAM)
- se a memória foi mais utilizada pode ser que, ele vai acionar a parte de swap.
- quando se usa a swap, o sistema fica mais lento, é para ser usada em emergência, falta de recursos
- servidores de produção, é bom que tenha swap.
- não pode entrar na area de swap em máquinas de produção

# cat /proc/swaps 
Filename				Type		Size	Used	Priority
/swapfile                               file		2097148	0	-2





  
































processes blocked on I/O


blocks out



pstree, ps




blocks in






