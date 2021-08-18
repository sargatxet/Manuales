# Manual básico de configuración de un VPS basado en Ubuntu LTS en Vultr

## 1. Crear nueva cuenta en Vultr.com

Cuando no se dispone de una cuenta en Vultr, lo mejor es empezar con una cuenta de pruebas con $100 disponibles para hacer pruebas durante 1 més. Para ello hay que usar el siguiente enlace:

[Registro en Vultr con $100 de regalo](https://www.vultr.com/?ref=8598591-6G)

Para crear una cuenta es necesario tener una dirección de correo electrónico, y, en caso de querer continuar en el servicio tras las pruebas hay que realizar KYC para realizar un primer pago mediante tarjeta de crédito o PayPal (_solo es necesario para la primera vez, luego ya se puede pagar en BTC_).

---

## 2. Crear un servidor basado en Ubuntu LTS

1.  Para la selección del servidor existen multitud de opciones, dependiendo de la capacidad de la CPU, de la RAM necesaria, de la capacidad de almacenamiento pero podemos destacar por su coste los servicios de **Cloud Compute** o **High frequency**.

2.  También es posible la elección de la ubicación del VPS a lo largo de varios paises, y solamente hay que tener en cuenta la disponibilidad del sistema deseado y que si necesitamos almacenamiento extra mediante **Block Storage** deberemos elegir _Los Angeles_ o _New York_.

    \*\*\*Por norma es mejor elegir un servidor cercano a nuestra ubicación para mejorar el rendimiento en la conexión con el servidor.

3.  Como S.O. elegiremos (salvo que el pool necesite otro diferente para su mejor funcionamiento) **Ubuntu 20.04 LTS**

4.  Elegiremos el tamaño necesario por CPU/RAM/SSD según el proyecto.

5.  Marcaremos que se habilite la IPv6.

6.  Hay que seleccionar la **SSH Key** de **Sargatxet** para que, en caso de no poder supervisar el servidor el responsable del proyecto, siempre haya un segundo operador con acceso al servidor.

    \*\*\*En caso de no haberlo hecho antes, añadiremos la clave siguiente

        ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDGk964IJqMYdbSp5XPcFrY5fynxVBkxFzdwVYiK2YNNpmSqvaA4IHZFBa+YFGkmB9zwbEDBXrqdKYEnyOr+x2tYW0gooTLRFhWgxY5d5uS5LhVUt7MPfuOxxHFlD9VWDi5KPVkYrQxbF4iwwGMOsGP85x0wenjQsN8Smw4ywylqUPGKQqkQHLFus9xF9YhPoLUxicGGZq+s/MOvmhkqmjl7rMl1Z0g7bxbBTM/tTGdIbVYTSEGAVde3faQFwX5yBAuyMmIS0lkRehxVJipMSX3gde+NnKyWB7/w9eE40idGa4ryl5Xi9iouWEBNx9ePyLoa8FUs1j8XMoBxpWsoyaHcL1zIgVzB+VbwxFix0Lhe/PVTSh34qRjX/MwomrT1j9jhru2+PultkmNZ6s+B6T/j8JEXdpH77WcNVVzVUl9qSWAjP3IsK1WqF8OOBjyfAZJLFvYeZW3ybr++tKpMMiRnZFi8dvJ4NcQtNmBCVt3FbmKJgaufgsVvepzRqJniCBN0Gec9PTmxKHLWbpZk8VvJlT3Kuh+ONwAMuMapBXzuuiwEX91xNLrTUQHec0KWgYAsVrjJkCBDe0uhWnOE6mv7nr8MLNwjtWIMbTMco39w36/VhlJl0k2ShP3iMFoX5y0yOTgVR4GhKEoAgt4HzCfufqJZE4rUDbEtzCYBzsxvQ== SargatxetCP

    \*\*\*Sería conveniente que cada operador tuviera su propia clave SSH para acceder a sus servidores, ya que si establecemos que solamente se pueda acceder con clave será mucho más seguro.

7.  Estableceremos el nombre del servidor (P.e.: PoolHarmony)

8.  Ya solamente nos queda desplegar el servidor y se nos proporcionarán los siguientes datos:

    - IP: _???.???.???.???_
    - Nombre usuario administrador: _root_
    - Contraseña de acceso para _root_: _??????????_

    A pesar de ser una contraseña muy fuerte es mejor siempre, una vez dentro del sistema, cambiarla por otra que pongamos nosotros.

## 3. Actualizar/Configurar servidor.

Accederemos al servidor como usuario _root_, bien con usuario/contraseña, bien con clave SSH y estableceremos las siguientes configuraciones:

1.  Cambiaremos la contraseña del usuario _root_:

        root@Poolharmony# passwd
        New password:
        Retype new password:
        passwd: password updated successfully

2.  Creamos el fichero de alias para facilitarnos la labor:

        echo "alias actualizar='apt autoremove -y && apt update && apt upgrade -y'" >> ~/.bash_aliases
        echo "alias df='df -h -t ext4'" >> ~/.bash_aliases
        echo "alias du='du -hs'" >> ~/.bash_aliases
        echo "alias free='free -m'" >> ~/.bash_aliases
        echo "alias puertos='netstat -an | grep \"LISTEN \"'" >> ~/.bash_aliases
        echo "alias ufws='ufw status numbered'" >> ~/.bash_aliases
        echo "export PATH=~/bin:$PATH" >> ~/.bash_aliases

    Tras esto ejecutaremos:

        . ~/.bash_aliases
        apt install -y net-tools

3.  Creamos el usuario con el que nos conectaremos a partir de ahora:

        root@PruebasPool:~# adduser operador
        Adding user `operador' ...
        Adding new group `operador' (1000) ...
        Adding new user `operador' (1000) with group `operador' ...
        Creating home directory `/home/operador' ...
        Copying files from `/etc/skel' ...
        New password:
        Retype new password:
        passwd: password updated successfully
        Changing the user information for operador
        Enter the new value, or press ENTER for the default
            Full Name []: Operador pool
            Room Number []:
            Work Phone []:
            Home Phone []:
            Other []:
        Is the information correct? [Y/n]
        root@PruebasPool:~#

4.  Configuramos el usuario:

    1.  Copiamos las claves ssh autorizadas al usuario operador

            mkdir /home/operador/.ssh
            cp ~/.ssh/authorized_keys /home/operador/.ssh/
            chown operador:operador -R /home/operador/.ssh

    2.  Nos logamos con el usuario:

            su - operador

    3.  Creamos su propio fichero de alias:

            echo "alias actualizar='sudo apt autoremove -y && sudo apt update && sudo apt upgrade -y'" >> ~/.bash_aliases
            echo "alias df='df -h -t ext4'" >> ~/.bash_aliases
            echo "alias du='du -hs'" >> ~/.bash_aliases
            echo "alias free='free -m'" >> ~/.bash_aliases
            echo "alias puertos='netstat -an | grep \"LISTEN \"'" >> ~/.bash_aliases
            echo "alias ufws='sudo ufw status numbered'" >> ~/.bash_aliases
            echo "export PATH=~/bin:$PATH" >> ~/.bash_aliases

    4.  Volvemos al usuario _root_

            operador@PruebasPool:~$ exit
            logout
            root@PruebasPool:~#

    5.  Añadimos al usuario al grupo de _SUDOERS_

            root@PruebasPool:~# usermod -aG sudo operador

5.  Modificamos el acceso al servidor para no hacerlo ni con el usuario **root** ni por el puerto estandard:

        root@PruebasPool:~# nano /etc/ssh/sshd_config

    En este fichero deberemos modificar la siguiente línea:

        ...
        PermitRootLogin no
        ...

    Y añadiremos estas opciones al final del fichero:

        Port 22
        Port 23132
        PasswordAuthentication no

    \*\*\*Esta segunda opción solamente la incluiremos si todos los usuarios tienen acceso **SSH Key** para.

6.  Reiniciamos el servicio **ssh** y comprobamos que se han aplicado correctamente las modificaciones:

        root@PruebasPool:~# service ssh restart
        root@PruebasPool:~# puertos
        tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN
        tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
        tcp        0      0 0.0.0.0:23132           0.0.0.0:*               LISTEN
        tcp6       0      0 :::22                   :::*                    LISTEN
        tcp6       0      0 :::23132                :::*                    LISTEN
        root@PruebasPool:~#

7.  Activamos el firewall solamente para los puertos que vamos a usar:

        root@PruebasPool:~# ufw allow 23132
        Rules updated
        Rules updated (v6)
        root@PruebasPool:~# ufw enable
        Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
        Firewall is active and enabled on system startup
        root@PruebasPool:~# ufws
        Status: active

            To                         Action      From
            --                         ------      ----
        [ 1] 23132                      ALLOW IN    Anywhere
        [ 2] 23132 (v6)                 ALLOW IN    Anywhere (v6)

8.  Reiniciamos el servidor

        reboot

\*\*\*A partir de ahora nos deberemos conectar con el usuario **operador**, el puerto **23132** y, en caso de haberlo configurado, con la **SSH Key**

9.  Actualizamos el sistema

        operador@PruebasPool:~$ actualizar
        [sudo] password for operador:
        ...

    Si nos pregunta que hacer con algún fochero de configuración (p.e. por el openssh-server) elegiremos **keep the local version currently installed**

10. Reinicamos el servidor de nuevo

        operador@PruebasPool:~$ sudo reboot

11. Repetimos el paso **9** para eliminar los paquetes innecesarios y nos salimos del servidor

12. Es conveniente, una vez establecida la base, crear un **Snapshot** en _Vultr_ desde el que podremos instalar una nueva instancia (de igual o superior potencia) ya con todos estos pasos configurados.
