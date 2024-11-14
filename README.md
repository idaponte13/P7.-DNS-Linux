# P7.-DNS-Linux

## Configura un sistema cun servidor DNS e un cliente alpine que cumpla os seguintes requisitos.

**Volumen por separado da configuración** 
Neste caso montaremos dous volumenes, un chamado `.conf` o cal contendrá a configuración do servidor BIND e outro chamado `.zonas` o cal montará o directorio das nosas zonas.   
```
volumes:
    - ./conf:/etc/bind/  # Montar la configuración del servidor BIND
    - ./zonas:/var/lib/bind/  # Montar el directorio de zonas
```