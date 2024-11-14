# P7.-DNS-Linux

## Configura un sistema cun servidor DNS e un cliente alpine que cumpla os seguintes requisitos.

>[NOTE]
>Ao final da explicación mostrarase a o arquivo .yml con toda a configuración xunta.

**Volumen por separado da configuración** 

Neste caso montaremos dous volumenes, un chamado `.conf` o cal contendrá a configuración do servidor BIND e outro chamado `.zonas` o cal montará o directorio das nosas zonas.   
```
volumes:
    - ./conf:/etc/bind/  
    - ./zonas:/var/lib/bind/  
```

Dentro da configuracion don noso servidor no archivo `named.conf` dentro do directorio `conf`, encontrase o seguinte codigo comentado para saber que fai cada cousa:  
```
zone "iago.int" {
    type master;  // O servidor é autoritativo e ten o control completo da zona "iago.int".
    file "/var/lib/bind/db.iago.int";  // Especifica o ficheiro de zona que contén os rexistros DNS para "iago.int".
    allow-query {
        any;  // Permite que calquera cliente poida realizar consultas a esta zona.
    };
};

options {

    dnssec-validation no;  // Desactiva a validación de DNSSEC para que o servidor non intente verificar sinaturas DNSSEC.
    
    directory "/var/cache/bind";  // Establece o directorio de traballo onde BIND almacenará ficheiros de caché e datos temporais.
    
    forwarders {
        8.8.8.8;  // Servidor DNS de Google para reenviar consultas.
        1.1.1.1;  // Servidor DNS de Cloudflare para reenviar consultas.
    };
    forward only;  // Obriga ao servidor a reenviar todas as consultas non autoritativas só aos `forwarders` definidos.

    listen-on { any; };  // O servidor escoita solicitudes en todas as interfaces de rede IPv4 dispoñibles.
    listen-on-v6 { any; };  // O servidor escoita solicitudes en todas as interfaces de rede IPv6 dispoñibles.

    allow-query {
        any;  // Permite que calquera cliente realice consultas ao servidor DNS de forma global.
    };
};

```

**Red propia interna para tódo-los contenedores**  
Nesta parte de networks configuraremos unha IP estática para o noso servidor DNS a cual utilizarán os clientes para poder conectarse a este.  
``` 
networks:
      bind9_subnet:
        ipv4_address: 192.28.5.1  # IP estática del servidor DNS
```

