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

