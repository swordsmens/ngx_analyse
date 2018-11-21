# ngx_analyse
nginx code ananlyse


chapter1.3.4 Linux内核参数的优化 
--------------------------------
>修改内核参数的方法：
>>(1)通过/et/sysctl.conf文件；
>>>修改/et/sysctl.conf对应参数；sysctl -p。
>>>-p[FILE], --load[=FILE] Load  in  sysctl  settings  from  the file specified or /etc/sysctl.conf if none given. 
>>(2)直接通过proc文件系统修改。

>>fs.file-max
>>>最大并发数，每一个来自client 的incoming connection都要占用一个fd,如下图所示：
>>>![image](https://raw.githubusercontent.com/dahaiyu/nginx_reading_notes/master/img_folder/chapter1/lsof_fd.png) 
>>>每一个incoming connection并不会新占用一个本机端口号,如下图所示：
>>>![image](https://github.com/dahaiyu/nginx_reading_notes/blob/master/img_folder/chapter1/netstat_1.png?raw=true) 
>>>即socket accept一个新链接时，Local Address不需要占用一个新端口号，只需要占用一个fd。

>>Q:Server端所能接受的incoming connections是否受限于端口号(65535)？
  
>>net.ipv4.tcp_tw_recycle
  
    Setting tcp_tw_recycle to 1 makes a Linux host drop TIME_WAIT connections much faster.  Instead of a predefined 2*MSL period 
    of 60s, the host will use a timeout based on RTT estimate.  For LANs, it is usually several milliseconds. 
  net.ipv4.tcp_tw_reuse
  
    Setting tcp_tw_reuse to 1 will make a host reuse the same connection quickly for outgoing connections. 
  net.ipv4.tcp_fin_timeout - INTEGER
  
    @Time to hold socket in state FIN-WAIT-2, if it was closed by our side. Peer can be broken and never close its side,
    or even died unexpectedly. Default value is 60sec.Usual value used in 2.2 was 180 seconds, you may restore
    it, but remember that if your machine is even underloaded WEB server,you risk to overflow memory with kilotons of dead sockets,
    FIN-WAIT-2 sockets are less dangerous than FIN-WAIT-1,because they eat maximum 1.5K of memory, but they tend to live longer.	
    
    @The length  of  time  in  seconds  it  takes to receive a final FIN before the socket is  always  closed.  
    This  is  strictly  a violation  of  the  TCP specification, but required to prevent denial-of-service attacks.
  
>>Q:client主动关闭，若迟迟没有收到server端的FIN, 则client 的传输控制块status什么时候会变为CLOSED?
  
