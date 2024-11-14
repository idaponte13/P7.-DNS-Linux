# P7.-DNS-Linux

## Configura un sistema cun servidor DNS e un cliente alpine que cumpla os seguintes requisitos.

>[!NOTE]
>Nos arquivos subidos .yml, conf/named.conf e zonas/db.iago.int poderemos velos con toda a configuración xunta e comentada.

**Volumen por separado da configuración** 

Neste caso montaremos dous volumenes, un chamado `.conf` o cal contendrá a configuración do servidor BIND e outro chamado `.zonas` o cal montará o directorio das nosas zonas.   
```
volumes:
    - ./conf:/etc/bind/  
    - ./zonas:/var/lib/bind/  
```

**Red propia interna para tódo-los contenedores**  
Nesta parte de networks configuraremos unha IP estática para o noso servidor DNS a cual utilizarán os clientes para poder conectarse a este.  
``` 
networks:
      bind9_subnet:
        ipv4_address: 192.28.5.1  # IP estática del servidor DNS
```

**Configurar Forwarders**  
Neste apartado configuraranse unha lista de servidores DNS aos que se reenviarán as consultas non resoltas localmente. No exercicio dicidironse Google e Cloudfare para isto.  
```
forwarders {
        8.8.8.8;  // Servidor DNS de Google para reenviar consultas.
        1.1.1.1;  // Servidor DNS de Cloudflare para reenviar consultas.
    };
```
**Crear Zona propia**  
    **Rexistros a configurar: NS, A, CNAME, TXT, SOA**  
Este apartado configurarase dentro do arquivo `db.iago.int` dentro do directorio de `zonas`.  
```
$TTL 38400	; 10 hours 40 minutes
@		IN SOA	ns.iago.int. some.email.address. (
				10000002   ; serial
				10800      ; refresh 
				3600       ; retry 
				604800     ; expire 
				38400      ; minimum 
				)
@		IN NS	ns.iago.int.
ns		IN A		192.28.5.1
test	IN A		192.28.5.4
www		IN A		192.28.5.7
alias	IN CNAME	Practica7
texto	IN TXT		ejercicio
```

**Cliente con ferramientas de rede**

Primeiro configuraremos no .yml o cliente cunha dirección dentro da mesma subrede e a dirección do servidor na parte de DNS.  
Unha vez feito isto poderemos acceder ao cliente facendo os comandos `docker compose up` para executar os contenedores e para entrar na terminal do contenedor creado co comando `docker exec -it Prac7_alpine /bin/sh`, no meu caso Prac7_alpine porque chameis asi ao contenedor do cliente. Posteriormente instalaremos dig para ver se podemos conectarnos de forma correcta. Para instalarlo executaremos o comando `apk update && apk add bind-tools`.

Por último faremos al consultas necesarios como o comando `dig ns.iago.int` ou `dig 192.28.5.1`. 
```
; <<>> DiG 9.18.27 <<>> ns.iago.int
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15843
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 8906064d2c684f2f01000000673631a4a0d937572e58c05a (good)
;; QUESTION SECTION:
;ns.iago.int.			IN	A

;; ANSWER SECTION:
ns.iago.int.		38400	IN	A	192.28.5.1

;; Query time: 0 msec
;; SERVER: 127.0.0.11#53(127.0.0.11) (UDP)
;; WHEN: Thu Nov 14 17:21:40 UTC 2024
;; MSG SIZE  rcvd: 84
```