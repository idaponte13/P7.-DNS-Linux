# P7.-DNS-Linux

## Configura un sistema cun servidor DNS e un cliente alpine que cumpla os seguintes requisitos.

>[!NOTE]
>Ao final da explicación mostrarase a o arquivo .yml, conf/named.conf e zonas/db.iago.int con toda a configuración xunta.

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
