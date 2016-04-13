# Meta
* Las referencias a páginas del libro se encuentran entre paréntesis:
	* RH229 V1 == (1.###)

<center>2016-04-05 (martes)</center>

# Introducción: Configuración teclado (1.XV)
* Mostrar configuración actual de teclado:
> \# localectl

* Mostrar configuración local:
> \# locale

* Cambiar idioma del sistema:
> \# localectl set-locale LANG=es_ES.utf8

* Cambiar teclado a español (es persistente):
> \# localectl set-keymap es

* También hay que cambiar el teclado en Gnome:
    1. Ir a Apps->System tools->Settings->Region and language
    * Añadir input souce: spanish
        * En la barra de tareas de Gnome, seleccionar spanish.
        
* Mostrar todos los idiomas disponibles:
> $ locale --all

* Cambiar el idioma solo para un comando en concreto:
> $ LANG=gl_ES.utf8 cal

## Notas varias sobre vim:
* _ZZ_ <- sale grabando independiente de idioma.
* _ZQ_ <- sale sin grabar independiente de idioma.

# Capítulo 1: Login local y remoto

## Configuración autenticación con claves SSH (1.8)

* La configuración SSH de usuario está en _$HOME/.ssh/_
* Conexión SSH con password:
> $ ssh desktop0

>>> La primera vez hay que aceptar la huella de la clave pública. Esto se almacena en el archivo _$HOME/.ssh/known_hosts_. A continuación solicitará password de usuario.

* Conexión SSH con claves:
    1. Crear par de claves (genera una privada y una pública):
    
        > $ ssh-keygen
        
    * Solicitará introducir una frase de paso para la clave privada. Por seguridad se debería establecer en todo caso.
  
            * Las claves generadas quedan almacenadas en: _$HOME/.ssh/id_rsa/_:
                * Archivo _id_rsa_ contiene la clave privada.
                * Archivo _id_rsa.pub_ contiene la clave pública. Esta clave se puede hacer pública sin problema, al contrario que la privada, la cual no debe de salir de nuestro equipo.
                
        * Copiar la clave pública al servidor al que queremos conectar. Desde dicho servidor, ejecutar:
        > ssh-copy-id student@desktop0
            
            * Con esto se añadiría el _id_rsa.pub_ anterior a una línea de _$HOME/.ssh/authorized_keys_ del servidor al que queremos conectar. 

### Extensión laboratorio conexión SSH con llaves

Se pretende que cuando se haga SSH contra untra máquina, en lugar de ejecutar _bash_, se ejecute el comando _hostname_ y luego cierre la conexión SSH:

1. Editar _$HOME/.ssh/authorized_keys_,
* Al principio de la línea de la clave en concreto(antes de _"ssh-rsa"_), añadir:
>_command="hostname"_


## Obteniendo ayuda de Red Hat (1.11)

Las suscripciones dan derecho a actualizaciones y soporte. Hay varios tipos:

* Estándar: ~300€ al año.
    * Soporte normal: 2 días laborables de tiempo de respuesta.
        * Soporte premium 1 hora de respuesta. 1000€/año.
* Solo actualizaciones: ~50€ al año.

### Comando redhat-support-tool

Tras introducir credenciales, permite:

* Hacer búsquedas en la base de conocimiento.
* Interactuar con el soporte de Red Hat.

### Soporte de Red Hat (1.14)

En cualquier interacción con el soporte de Red Hat, se ha de facilitar un archivo con la información (memoria, paquetes instalados, configuración de red, etc) de nuestra máquina. Dicho archivo se genera con:
> \# _sosreport_

* Se generará un archivo .tar.gz en _/var/tmp/_.

# Capítulo 2: Jerarquía de sistema de archivos (1.20)

En unix hay dos estándares de sistemas de archivos:

* Tipo _System V_: Por ejemplo _Linux_.
* Tipo _BSD_: Por ejemplo _FreeBSD_.

Gerarquía de archivos en Linux:
>
        /bin -> binarios
        /boot -> arranque del sistema (kernel, grub, etc). No puede estar en partición sobre LVM ni raid.
        /dev -> dispositivos (ratón, teclado...).
        /etc -> configuraciones en modo texto.
        /home -> raíz de de los usuarios.
        /lib -> bibliotecas de funciones.
        /media -> puntos de montaje creados automáticamente por Gnome o Kde. 
        /mnt -> punto de montajes creados manualmente.
        /opt -> instalaciones de grandes aplicaciones (oracle, sap...).
        /proc -> directorio virtual. Generado por el kernel en tiempo real.
        /root -> raíz del usuario root.
        /sbin -> binarios que solo puede ejecutar toot.
        /srv -> datos que se publican al exterior (FTP, web, etc). Se ha añadido recientemente, Red Hat no lo sigue demasiado.
        /sys -> es un "spinoff" de /proc/. Saca info de buses, kernel, módulos, dispositivos de almacenamiento etc. Separa ciertas cosas de /proc/.
        /tmp y /tmp/var/ -> temporales.
        /usr -> eraquía secundaria.
                /usr/local -> soft instalado de manera manual, sin usar gestión de paquetes.
                Actualmente se ha unificado /bin y /usr/bin, sbin y /usr/sbin. Tienen enlaces unos a otros.
        /var -> ficheros variables persistentes en el tiempo, pero que pueden cambiar durante su vida útil.

## Gestión de ficheros usando utilidades en línea de comandos (1.26)

* Se  pueden mover o copiar varios ficheros a la vez:
> $ mv fichero1 fichero2 fichero3 /directorio/
        
* Crear directorio junto con su ruta completa:
> mkdir -p /dir1/dir2/dir3/

* Crear varios directorios junto con su ruta completa:
> mkdir -p company/{desarrollo,markeing,finanzas}/{personal,recursos}

<center>2016-04-06 (miércoles)</center>

## Gestión de enlaces entre archivos (1.34)

* Comando "stat" muestra los metadatos de un fichero. Por ejemplo:
> $ stat archivo1

    * Entre otra, muestra información sobre fechas:
            * atime: Cuando se ha leído la última vez. atime solo se actualiza si ha pasado más de un día o si se ha modificado el contenido.
                * mtime: Cuando se ha modificado.
                * ctime: Cuando se ha cambiado.

* El servicio _audit_ audita quien y cuando accede a cada archivo. Por defecto no está habilitado.

Existen dos tipos de enlaces, simbólicos y duros:

### Enlaces simbólicos
En inglés **Simbolic link**. Se consigue con el siguiente comando:
> $ ln -s original simbolico 

Características reseñables de los enlaces simbólicos:

* Cuando se hace _ls_, los enlaces están marcados con una "l" al inicio de la parte de permisos: "lrxwrxwrxw". 
* Los permisos efectivos son los del archivo original. Aunque también es posible establecer permisos en el simbólico.
* Si se borra "original", "simbolico" quedaría huérfano, por lo que al intentar acceder a él daría error. Sí se puede acceder normalmente a "duro". Si se volviera a crear "original", el enlace simbólico "blando" volvería a apuntar a "original", o sea, apunta directamente a nombres de archivo.
* No es buena práctica usar rutas relativas al hacer enlaces simbólicos, ya que si lo mueves el archivo al que se enlaza se perdería el camino.

### Enlaces duros
En inglés **Hard link**. Se realiza del siguiente modo:
> $ ln original duro

Características reseñables de los enlaces duros:

* Aquí no se ve nada al hacer un _ls_ al lado de los permisos. Pero sí hay un "2" en la segunda columna, justo a la derecha de los permisos. Dicho número se corresponde con el número de enlaces duros que apuntan al archivo.
* Tanto el "duro" es enlace de "original", como "original" es enlace de "duro". 
* En cuanto a metadatos, si se lee el original, se actualizará por ejemplo  también el mtime del enlace duro, ya que los metadataos están físicamente al final del contenido del archivo.
* Si se borra "original", "duro" **no** quedaría huérfano, por lo que se podría acceder a él.

# Capítulo 3: Usuarios y grupos (1.40)

## Usuarios

* Todo fichero pertenece a un usuario y un grupo.
* Todo proceso pertenece a un usuario en particular.
* El comando "id NOMBRE_USUARIO" muestra el grupo y _ids_ del usuario que se le solicita.
* Se puede cambiar el nombre de un usuario en caliente ya que el kernel funciona internamente con el id. Lo mismo con los nombres de ficheros.
* Un nombre de usuario tiene que empezar siempre por una letra.
* Si se borra un usuario sus ficheros se quedan con el _uid_ que tenía originalmente el usuario. Dicho archivo se queda con el _uid_ con número en lugar del nombre de usuario.
* Buscar en el sistema archivos huérfanos:
> $ find / -nouser -or -nogroup 2>/dev/null"
* Los usuarios se encuentran en _/etc/passwd_
* _/etc/passwd_ puede ser leído por todo los usuarios, Cada entrada de usuario contiene los siguientes campos:
    1. Nombre de usuario.
    * Contraseña: Hoy en día no se almacena aquí, las contraseñas están en _/etc/shadow_ (Solo accesible por _root_).
    * UID: ID de usuario.
    * GID: ID de grupo principal.
    * GECOS: Comentario.
        * Directorio home: Datos personales y configuraciones del usuario.
        * Shell: Programa que se inicia cuando el usuario hace _login_. Si se pone como shell "/sbin/nologin" no se permite el login a consola al usuario, pero sí puede usar otros servicios, por ejemplo el correo.

## Grupos

* Un usuario siempre debe tener un grupo primario.
* Red Hat no añade al grupo _users_ los usuarios recién creados, (lo cual se supone que es el estándar). en su lugar, crea un grupo igual que el nombre de usuario, el cual pasa a ser su grupo primario.
        
* Los grupos se encuentran en _/etc/group"_. Cada entrada de grupo contiene los siguientes campos:
        1. Nombre del grupo.
        * Password de grupo: Puede haber un password de grupo. Ref: d
        * GID: ID de grupo.
        * Lista de usuarios dentro de ese grupo (separados por comas).

[1]: http://unix.stackexchange.com/questions/93123/typical-use-case-for-a-group-passwor        "Contraseñas de grupo".

## Acceso como super-usuario (1.44)
Es posible acceder como super-usuario desde otro usuario logueado de dos formas:

1. Comando "su usuario":
    * Permite cambiar a un nuevo usuario. Si se usa "su - usuario" se carga el entorno (variables de entorno, directorio actual, etc) del nuevo usuario.

* Comando "sudo":
    * Permite ejecutar un comando con permisos de otro usuario.
    * Normalmente se usa para ejecutar comandos como root, para ello hay que configurarlo antes en _/etc/sudoers_.
        * Para editar _sudoers_ se usa "visudo", al intentar guardar cambios, comprueba antes la sintaxis, de este modo se evita generar un _sudoers_ erroneo.
        * El editor que usa es el establecido en la variable de sistema $EDITOR, por defecto es "vi" (se puede cambiar: "export EDITOR=/usr/bin/gedit").
        * Cada entrada (línea) en _sudoers_ tiene el siguiente formato:

            > USUARIO ORIGEN=USER_AL_QUE_PUEDE_CAMBIAR COMANDO_CON_PARÁMETROS
            
            * Tiene mayor preferencia la última línea, de manera secuencial. Se evalúa el fichero entero.
            
                        * Ejemplo 1 (se permite ejecutar fdisk):
                        
                            > fernando 192.168.1.145=(root) "/usr/sbin/fdisk /dev/sdb"
                            
                        * Ejemplo 2 (se permita ejecutar pass con todo, expto con root):
                        
                                > student ALL=(ALL) "/usr/bin/passwd *, !/usr/bin/passwd root"

## Administrar usuarios locales (1.51)
* Borrar usuario:
> \# userdel -r juan
    * Si no se indica "-r no se borra su directorio $HOME.
* UIDs:
>
    0 reservado para root
    1-200 reservado para servicios de sistema.
    201-999 reservado para servicios de sistema creados por usuarios.
    +1000 uso genérico. Los nuevos usuarios empiezan en +1000 de forma predeterminada.

* La configuración de login de usuarios está en _/etc/login.defs_.
* El contenido de "/etc/skel" lo copia al home del usuario cuando se da de alta el mismo.
* Uso de GUI para gestión de usuarios en Gnome:
        1. Instalación:
        > \# yum install system-config-users
        * Se ejecuta desde: "Applications->Sundry->Users and Gropus" (Sundry en castellano está traducido como "varios").
        * Por defecto solo muestra usuarios a partir del uid 1000. Esto se puede cambiar en "Edit->presferences->Hide system users and groups".
        * Los cambios que se hagan en esta herramienta son inmediatos.
                
* Existen dos binarios en CLI para gestionar usuarios:
    * useradd: Es el la herramienta nativa incluida en el sistema.
    * adduser: Es un script escrito en perl que usa useradd como back-end. Adduser es mś amigable e interactivo. No hay diferencias de funcionalidad entre ambos.

## Administración de contraseñas de usuario (1.60)

* Las contraseñas están en "/etc/shadow". Campos de cada línea:
        1. Nombre de usuario.
        * Contraseña:
                1. Algoritmo usado para la huella (hash): 1:md5, ,5:sha-256, 6:sha-512. Esto se configura en _login.def_.
                * Hash de la contraseña. Al ser usado algoritmo un unidireccional, a partir de esta huella no se puede recuperar una contraseña.
                * Número aleatorio (llamado semilla o _salt_). El _salt_ se une a la contraseña a la hora de calcular el _hash_. De este modo se evita que dos usuarios con la misma contraseña acaben con el mismo _hash_.
        * Fecha de último cambio de contraseña: Días desde 1 enero 1970.
        * Número de días antes de que la contraseña pueda ser cambiada.
        * Número de días tras los que caduca la cuenta.
        * Número de días que se usarán para dar aviso previo a la caducidad de la cuenta.
        * Número de días que la cuenta permanece activa tras expiración de contraseña.
        * Fecha de expiración de la cuenta.
        * Reservado para futuro uso.

## Servicios gestión identidad (1.65)
* Proveen autenticación y autorización.
* IPA (Identity, Policy, and Audit):
    * Fue discontinuado por Red Hat, siguió su desarrollo como freeIPA. A posterior volvió IPA a Red Hat bajo el nombre de  _Red Hat Identity Management_ (IdM)[1]. 

* Herramientas para configuración de gestión de identidad:
    * Herramienta cli: _authconfig_.
    * Herramienta curses: _authconfig-tui_.
    * Herramienta gtk: _authconfig-gtk_. Se puede lanzar también con "_system-config-authentication_".
        * Estas herramientas escriben sobre _ldap.conf_ y otros archivos que se encuentran a partir de la páina 1.65.
        * El demonio sssd debe ser habilitado y lanzado antes de que el sistema pueda usarlo.

[1]: https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html-single/Identity_Management_Guide/        "Identity Management Guide"

# Uso de documentación general del sistema (X.XX)
* _man_: Interfaz de los manuales del software instalado.
    * Tiene varios tipos de sección. Las de más utilidad básica son:
        * 1: Programas y scripts.
        * 5: Formatos de fichero y convenios.
        * 8: Órdenes de administración de sistema.
    * Ejemplo de uso:
    > $ man 5 fstab

* Búsqueda en los manuales y documentación:
    *  Buscar en las descripciones cortas y páginas de manual
    >$ man -k bluetooth
    * Mostrar descripción de un comando:
    > $ whatis ls
    * Buscar en páginas del manual y en las descripciones:
    > $ apropos ls

* _whatis_ y _apropos_ usan la base de datos mandb
    * mandb se actualiza una vez al día vía cron. Se puede forzar la actualización con el comando "mandb". Antes de _Red Hat 7_ se actualizaba con "makewhatis".

* _info_: Documentación estructurada en ramas, con formateado similar a una página web. Existe "pinfo", el cual añade colores.

* _locate_: Permite localizar archivos y directorios en todo el sistema.
    * Su base de datos que se actualiza una vez al día desde cron. Se puede forzar su la actualización con "updatedb".

* _--help_; La mayoría de los comandos se obtiene con esta opción:
> $ ls --help

* _/usr/share/doc/NOMBRE_PAQUETE_: En dicho directorio suele haber documentación y templates propios de los paquetes instalados.

<center>2016-04-07 (jueves)</center>


# Capítulo 4: Permisos de archivo (1.81)
## Consulta de permisos de archivos

* Mostrar permisos de un archivo:
> $ ls -l archivo1

    > -rw-r--r-- 1 root root 841 feb 23 01:55 archivo1

* Mostrar permisos de un directorio:
> $ ls -ld directorio1/

    > drwxr-xr-x 2 root root 4196 feb 24 20:33 directorio1/

* Definición de los permisos:

    * (-rw)(r--)(r--)(.)
    * (user)(group)(others)(SE)
        * r <- Leer.
        * w <- Escribir (incluye crear, modificar y borrar).
        * x <- Permiso de paso a en directorio y ejecución en archivo.
        * . <- Existe contexto de SELinux.

## Modificación de permisos de archivos  

* Modificación de permisos de un archivo/directorio:
> $ chmod ug+rwx archivo1
  * En donde "ug" puede ser:
                u <- usuario
                g <- grupo
                o <- otros
                a <- todos (u+g+o)

* Modificación de permisos de un archivo/directorio en formato numérico:
> $ chmod 770 archivo1

    * Para calcular el número se realiza la suma de (4,2,1) de cada parte:
            * (rwx)(rwx)(---)
            *  421  421  421

* Modificación de permisos recursivamente:
> $ chmod -R 770 directorio1
    * Se puede dar permiso de paso a directorios pero no ejecución a ficheros:
    > $ chmod -R u+rwX directorio".
        * La "X" solo afecta a directorios. Esto no funciona con "r" ni "w", o sea no se pueden usar con mayúsculas para conseguir lo mismo.

## Cambio de propietario o grupo de archivos

* Cambio de usuario:
> $ chown student archivo1

* Cambio de grupo:
    * Con _chown_:
    > $ chown :students archiveo1
        * También funciona con un "." en vez de ":".
    * Con _chgrp_:
    > $ chgrp students archivo1

* Cambio de usuario y grupo simultáneamente:
> $ chown student:students archivo1

## Permisos especiales (1.87)

Los permisos especiales son _setuid_, setgid_ y _sticky bit_:

### setuid y setgid
Se comportan diferente si se aplican sobre archivos o directorios:

* **Permiso "s" en archivos**: Se hereda el permiso de ejecución usuario (setuid) o grupo(setgid) primario. De este modo un usuario puede ejecutar como _root_ aunque no lo sea. Por ejemplo:
> $ ls /usr/bin/passwd -> (-rws)(r-x)(r-x)


* **Permiso "s" en directorios**: Se le conoce como **grupo colaborativo**: Si en un directorio se puede entrar y escribir, lo que se crea o copia en él (no con mover, ya que mover arrastra permisos) queda con el grupo del directorio (aunque sí se mantiene el usuario del creador original).

### Sticky bit

** Permiso "t"**: En caso de ser necesario hacer un _o+w_, con _sticky bit_ se consigue que solo el propietario pueda borrar o renombrar los archivos y subdirectorios contenidos. De modo que _otros_ lo pueden modificar.

Estos permisos especiales se pueden aplicar a:

* ficheros:    (--s)(--s)(---) (a un fichero se le podría poner o+s pero no haría nada)
* directorios: (---)(--s)(--t) (a un fichero se le podría poner u+s pero no haría nada)
 
### Establecimiento de los permisos especiales
 
* Se establecen con _chmod_. Por ej:

> \# chmod u+s archivo1

> \# chmod g+s archivo1

> \# chmod +t directorio1

* Aplicación de manera numérica:

> (--s)(--s)(---)

> (--4)(--2)(--1)

* En caso de que se haga: _chmod 7000 archivo1_, para indicar que a parte del "+t" no hay un "o+x", se queda la "T" mayúscula:

> (---s)(--s)(--T)

* Ejemplos de aplicación de permisos especiales de manera numérica:

> chmod 4777 archivo (setuid)

> chmod 2777 archivo (setgid)

> chmod 1777 directorio (sticky bit)


### Gestión de permisos por defecto de archivos

El comando **umask** controla los permisos por defecto. Ejecutando:
> $ umask

Devuelve la máscara configurada por defecto:
> 00002

* El valor por defecto de _umask_ para el sistema se configura con:
> $ echo "umask 007" >> /etc/bashrc

* El valor de _umask_ para un usuario en concreto se configura con:
> $ echo "umask 007" >> ~/.bashrc

Para calcular los permisos efectivos que se aplican a los nuevos archivos recién creados, se resta la máscara a 777 o 666:

* Directorios:  777 - 022 = 775

* Archivos: 666 - 022 = 644 (esto implica que no se pueden establecer ejecutables por defecto, esto es así por temas de seguridad).

### Listas de Control de Acceso POSIX (ACLs) (1.93)

Con ACLs un archivo o directorio puede tener más de un propietario y grupo, cada uno con sus permisos.

Al hacer "ls -l", los archivos o directorios que tengan ACLs aplicadas aparecerán con un "+" tras los permisos estándar:
> -(rw-)(rw-)(r--)**+**

* Una vez que hay establecidos ACLs en un archivo, el (---) del grupo no se corresponde con el grupo, es la máscara. La máscara es un valor que se actualiza automáticamente cuando se hace _setfacl_. Muestra los permisos máximos establecidos en las ACLs de ese archivo.

#### Uso básico

* Obtener detalle del ACL de un archivo:

> $ getfacl fichero1

* Añadir ACL para que el usuario _student_ tenga permisos rw sobre un fichero:

> $ setfacl -m u:student:rw fichero1

* Eliminar todas las ACLs de un fichero:

> $ setfacl -b fichero1


* Borrar permisos ACL de un usuario:

> $ setfacl -x student fichero1


#### Máscaras de ACLs (1.97)

La máscara indica el permiso máximo que tienen las entradas de ACL de los siguientes actores:

* Grupo del propietario.
* Usuarios en ACL.
* Grupos en ACL.
* Grupo de root.

Un permiso que tenga uno de estos usuarios solo sería efectivo si también estuviese en la máscara.

La máscara no restringe a los siguientes usuarios:

* Propietario del archivo.
* Otros usuarios.
* Usuario root.

* Modificar permisos a través de la máscara de ACL:

> $ setfacl -m m::rx- file

Al hacer _getfacl_ aparecería un "#effective:***" al lado de cada entrada, en donde se indica el permiso real que tiene dicha entrada teniendo ya en cuenta la máscara.

* Cuando se añada una nueva entrada al ACL se recalcularía la máscara y ya sí que tendría "+x" de nuevo.

* Con la opción -n de "setfacl" se fuerza a que no se recalcule la máscara (pag. 1.103).

* Las entradas de ACL las aplica de modo que empieza por arriba, cuando una de permiso ya para de aplicar las inferiores. Si un usuario está en dos grupos de un ACL, en uno de ellos deniega y otro permite, tendrá acceso al fichero independientemente del orden en el que estén los grupos.


#### ACLs en directorios:

* **Permisos default**: Si se establece en directorios, los permisos se heredan. Cuando se genera contenido dentro de ellos este tienen las mismas ACLs que el directorio.

Asignar permisos por defecto: 

> $ setfacl -m d:u:student:7 directorio/ 

  > * El carácter "d" que precede a "u" es la que indica que es un permiso default. Tras esto, al hacer _getfacl_ al directorio, aparecen entradas "default".


* Al crear un sudirectorio dentro de un directorio padre con ACLs default, este subdirectorio hereda el ACL default del padre. Cuando se cambia los defaults del padre, los del subdirectorio no se modifican.

#### Otras consideraciones sobre ACLs:

* Para aplicar ACLs recursivamente a directorios (pag. 1.102):
> $ getfacl directorio | setfacl -R --set-file=- directorio/

* Al copiar archivos con ACLs a un sistema de archivos ntfs o fat, se pierden las ACLs ya que esos sistemas no son POSIX. Empaquetadores/compresores/backups puede que sean compatibles con ACLs, habría que revisar cada caso.

# Capítulo 5: SELinux (1.111)

* Es un mecanismo de seguridad del Kernel Linux (introducido hace unos 15 años). Desarrollado por la NSA junto a Red Hat.

* Es lo primero que se ejecuta tras cargar el Kernel en memoria. Para controlar todos los recursos (usuarios, procesos, ficheros, sockets de red, dispositivos...).

* Tiene una base de datos de perfiles en la cual figura qué se permite ver a cada recurso. Todos los recursos están etiquetados con un contexto de SELinux. Hay gran cantidad de perfiles ya establecidos.
    * Por ejemplo: apache solo puede acceder a puertos etiquetados como "http_port".

* Con configuraciones estándar, SELinux está muy estable. En caso de que queramos cambiar por ejemplo un puerto de Apache al 81, no se permitiría el acceso.

* Cuando SELinux bloquea algo, manda alerta al _log_ de _audit_, es una línea de tipo "type=AVC".

* Al crear un archivo se queda con un contexto (etiqueta) de su directorio, cuando se mueve a a otro directorio se queda con el contexto del directorio original.
    * Cuando se copia no pasa esto, ya que se queda con el contexto del destino.

* El contexto depende de donde está un archivo y de su nombre.

* El contexto SElinux de archivos se puede ver con "ll -Z".

* El contexto SElinux de procesos se ve con "ps -Z".
                
* Para restaurar un conexto de un fichero: "restorecon -v archivo"

* SELinux añade metadatos propios a cada archivo.

* El contexto son 4 campos. El más importante es el 3º, casi siempre se trabaja con él.

    1. Tipo de usuario: Por defecto están sin confinar. Siempre acaba en "2_u".
    * Rol/papel: admin. apache, samba, etc. Siempre acaba en "_r".
    * Tipo de recurso: Todos los recursos están etiquetados. De modo que algo que tenga una etiqueta, podrá acceder a todo lo que tenga esa misma etiqueta. Un recurso solo puede tener una etiqueta, pero a una etiqueta pueden acceder varios recursos. Esto siempre acaba en "_t".
    * No se utiliza. Es para un modo de funcionamiento de SELinux que RH no soporta.
    
* SELinux por defecto no deja seguir enlaces simbólicos, pero se puede habilitar.

* "SELinux Alert Browser" es la utilidad para entorno gráfico que muestra alertas de SELinux vía popup.

## Administrar SELinux

### Archivo de Configuración
La configuración del estado de SELinux en el sistema está en: "/etc/selinux/config". Puede estar en tres estados:

* SELINUX=enforcing: SELinux activo completamente.

* SELINUX=permissive: igual que _enforcing_ pero al final deja pasar todo, no impide nada, solo loguea.

* SELINUX=disables: desactivado.

Es posible el cambio en caliente entre _enforcing_ y _permissive_. Esto es útil para ir haciendo pruebas tras hacer configuraciones y comprobar si funciona ok.

Para cambiar de _enforcing_ o _permissive_ a _disable_ hay que reiniciar.


* Mostrar estado de SELinux en el sistema:
> $ getenforce

* Cambiar estado en caliente: 
> $ setenforce 1|0


### Modificación de contextos SELinux

* Añadir una regla "customizada" para conseguir por ejemplo que _/srv/miweb/_ sea etiquetado como Apache para que sea válido como directorio de archivos web:
> \# semanage fcontext -a -t "httpd_sys_content_t" '/srv/miweb/(/.*)?' 

    > * Se usa la expresión regular para hacerlo recursivo.

    * A continuación habría que aplicarlo:

    > \# restorecon -RFv /srv/"
    
* Añadir puerto a la etiqueta de apache para que pueda acceder apache a dicho puerto:
> \# semanage port -a -t http_port_t -p tcp 666

                
* Mostrar las reglas "customizadas". Por defecto en el sistema hay más de 5.000 reglas pre-establecidas.
> $ semanage fcontext -lC

### Booleanos de SELinux

Permiten hacer activar o desactivar funcionalidades. Por ejemplo:
* Que Apache siga links o no.
* Permitir que ciertos procesos accedan a /home (por ej FTP).

#### Configuración boleeanos:

* Listar booleanos:
> $ semanage boolean -l
    
    > Muestra: etiqueta (estado_actual, estado_al_inicio_de_la_maquina) descripción.

* Listar booleanos customizados:
> semanage boolean -lC

* Mostrar estado de booleano:
> $ getsebool httpd_can_network_connect_db

* Cambiar estado de booleano:

> $ setsebool -P ftp_home_dir on

> * La opción "-P" es para que el cambio sea persistente, ya que por defecto no lo es y al reinicar la máquina se perdería esta configuración.

<center>2016-04-08 (viernes)</center>


### Troubleshooting SELinux (1.129)

* El paquete **setroubleshoot-server** monitoriza el _audit.log_ y cuando encuentra una línea tipo _AVC_, la manda a _/var/log/messages_. Para ver esta información:
 
    * En modo gráfico: _policycoreutils-gui_ es una utilidad que muestra un _popup_ cuando hay una alerta de SELinux, mostrando información y posibles soluciones.
        * En Gnome aparecerá en "Applications->System Tools->SELinux Administration".



    * En consola: Los mensajes de _SELinux_ se guardan en _/var/log/audit.log_. Los que genera SELinux son tipo "AVC":
    > $ grep ^type=AC /var/log/audit.log

        * Para ver la misma información en consola que en el _popup_ de entorno gráfico:
        > $ grep sealert /var/log/messages

        > En la linea mostrará un "run sealert -l ********". Ejecutando dicho comando mostrará la misma información que da el _popup_ modo gráfico.

#### Documentación de SELinux
Por defecto no se instala la ayuda completa de SELinux (etiquetas, booleanos, ejemplos, etc.).

* Buscar páginas de manual de selinux:
> $ apropos selinux
    
    > Muestra solo una página, aunque en realidad existen ~800.

* Instalar documentación de SELinux:
> \# yum install -y selinux-policy-devel

* Actualizar base de datos de man:
> \# mandb

* Listar número de páginas de manual de selinux:
> $ apropos _selinux | wc -l

    > 778

# Capítulo 6: Gestión de procesos (1.142)

* Mandar una señal a un proceso (PID):

> $ kill -9 PID

> $ kill -KILL PID

* Principales señales:
    * 1 (HUP): Releer los ficheros de configuración y aplique los cambios en caliente (suele ser lo mismo que un service MI_SERVICIO reload).
    * 9 (KILL): Muerte incondicional (sin auto limpiado).
    * 15 (TERM): Muerte normal (con auto limpiado).
    * 18 (CONT): Continuar si el proceso se encuentra parado.
    * 19 (STOP): Congelar/dormir (ctrl+z). Cuando se quiera que vuelva a funcionar, se usa señal 18).

        * Puede ser más interesante usar los nombres de las señales en lugar de los números, ya que puede que en algún sistema, por ejemplo _Freebsd_, una señal no tenga el mismo número que en linux.

## Otros comandos para la gestión de procesos

* Lanzar un proceso en _background_:
> $ xeyes &

* Continuar (CONT) ejecutado en _background_ procesos que se encuentran parados (STOP):
>  $ bg 

* Matar todas las instancias de un programa refiriéndolo por nombre:
> $ killall xeyes

* Listar tareas activas de la sesión actual:
> $ jobs -l


* Matar una ventana de entorno gráfico:
> $ xkill
    * Esto pondrá el cursor del ratón en modo "clavaera" y cualquier ventana que hagamos click, la matará.

* Ver conexiones en el sistema (-f muestra el origen):
> $ w -f

## Monitorización de procesos (1.148)

* Mostrar carga del sistema:
> $ cat /proc/loadavg

    > 0.87 0.78 0.86 3/875 31889
    
    * Valor 1: carga último minuto
    * Valor 2: carga últimos 5 minutos
    * Valor 3: carga últimos 15 minutos
    
    * No es un número absoluto, depende del número de _CPUs_ que tengamos. Una carga de 4 con 4 _CPUs_ es lo mismo que una carga de 1 con una _CPU_. Para determinar la carga media de CPU, dividir el _load average_ por el número de CPUs.

* Monitorización de procesos en tiempo real:
> $ top

    * Campos línea de CPU:
        1. Espacio usuario.
        * Espacio sistema.
        * Tareas con mayor prioridad de lo normal.
        * Idle.
        * Waiting: Esperando a que le llegue información a través de disco o red. En caso de ser alto, indica cuello de botella.
        * Interrupciones de hardware.
        * Interrupciones de software.
        * Stolen: CPU robada por un hipervisor para gestionar máquinas virtuales
        
* Obtener información sobre la arquitectura de la CPU:
> $ lscpu
    * CPUs_lógicas=threads_per_core*cores_per_socket

* Mostrar el tiempo que estuvo el sistema en funcionando:
> $ uptime


## Gestión de prioridades con nice

* El sistema de prioridades va de 0 a 139
    * 0 = más prioridad
    * 139 = menos prioridad
    
* Por defecto todo se lanza con prioridad 120.

* El usuario no tiene acceso a modificar estas prioridades, si no que se hace a través de **nice**.

* El usuario puede subir el _nice_, pero no bajarlo. O sea, cuanto más _nice_, menos prioridad (menos recursos se consumen).

* _root_ sí que puede bajar el _nice_ (hasta -20). O sea, un usuario puede subirlo a 10, pero no bajarlo de nuevo a 8.

* Por defecto todo se lanza con _nice_ "0" lo cual es es prioridad 120.

### Uso de nice

* Lanzar proceso con un nice en concreto:
> $ nice -n 6 xeyes

* Cambiar el nice de un proceso ya lanzado:
> $ renice -n 8 xeyes


* _top_ llama RealTime (RT) a las prioridades. Va de 0 a 39.

* Con _top_ también se puede hacer un _renice_: con "r".

* En la herramienta gráfica (Gnome) de procesos se puede hacer click derecho y cambiar el nice en la opción de "Change Priority->Custom".


# Capítulo 8: Creación y montado de sistemas de archivos (1.191)

Existen dos formatos en los que la información se guarda en disco:

* **Formato MBR**:
    * Primeros 512 bytes
        * 446 bytes - sector de arranque (por ej grub).
        * 64 bytes - 4 entradas de tabla de particiones. No se puede tener más de 4 particiones primarias. Para solucionarlo se convierte una (y solo una) de las particiones primarias en extendida. Esta partición extendida tiene que ocupar todo el disco. O sea, una vez creada la extendida no se pueden crear más particiones. Habría que crear de última (4ª) la extendida. Quedando como máximo 12 particiones en la extendida y las 3 primarias.
        * 2 bytes - aa5z: marca de final.
    * Tecnología MBR permite gestionar discos hasta 2TB.

* **Formato GPT**:
    * Es una evolución del MBR. Forma parte del estándar UEFI.
    * Compatible con Red Hat desde la versión 7. EN RH5 y RH6, el MBR lo escribía después del primer MB por lo que al pasar a _GPT_, habría sitio para el _GPT_ al inicio del disco, ganando así compatibilidad con ambos formatos..
    * Permite discos de hasta IotaBytes.
    * Primer MegaByte:
        * 512 bytes: Copia en formato _MBR_ (por compatibilidad hacia atrás).
        * Cabeceras.
        * Tabla de particiones (hasta 128 particiones primarias). De las cuales las 4 primeras las podría leer en una máquina con soporte solo _MBR_.
    * Final del disco:
        * Repetida la tabla GPT (o sea el 1er MB). De este modo hay redundancia.
    * Para gestionar discos, en RHEL7 existe la utilidad "gdisk".

## Gestión de particiones

* Referirse a una partición:
    * Por dispositivo en /dev/

        * /dev/vdb <- disco
        * /dev/vdb1 <- partición

    * Por UUID (identificado único universal de partición). Es ideal montar siempre por _UUID_ para evitar cambios de asignación en /dev/.

* Listar particiones de disco:
> \# lsblk

* Formatear disco:
> \# mkfs /dev/XXXXX
    * xfs es el estándar por defecto de RHEL7

* Mostrar particiones formateadas con sistema tipo POSIX. Aparecen los UUIDs.
> \# blkid

* Montar todo lo que haya en /etc/fstab
> \# mount -a

* Mostrar tamaño particiones
> $ df -hT

* Ver quien está usando un punto de montaje (útil en caso de que no nos deje desmontarlo).
> \# lsof /datos/

* Acceso a dispositivos removibles (por ejemplo memorias USB):
    * /run/media/USER/LABEL

### Particionar disco con fdisk

* Iniciar _fdisk_ pasándole como parámetro el disco:
> \# fdisk /dev/vdb

    * Mostrar la tabla de particiones:
    > fdisk> **p**
    
    * Crear nueva partición:
    > fdisk> **n**

        * Seleccionar primaria o extendida.
        * Seleccionar número de partición.
        * Seleccionar primer sector.
        * Seleccionar tamaño o último sector.

    * Guardar cambios:
    > fdisk> **w**
    
* Forzar al kernel para que relea la tabla de particiones:
> \# partprobe
    * En caso de que hiciésemos el particionado con alguna partición del disco ya montada, al guardar cambios en _fdisk_ se mostrará un "warning" que solicita reiniciar o ejecutar _partprobe_
    * _partprobe_ no funciona en RHEL 6 (en 4, 5 y 7 sí), esto era porque la versión de 6 tuvo un bug que no solucionaron.

## Administración espacio de intercambio (swap) (1.211)

* Mostrar particiones swap:
> \# swapon -s

* Formatear partición como swap:
> \# mkswap /dev/vdb2

* Activar particiones swap:
> \# swapon /dev/vdb2 (o _-a_ para activar todas las que estén en _/etc/fstab_)

* Desactivar particiones swap:
> \# swapoff /dev/vdb2

<center>2016-04-11 (lunes)</center>

# Capítulo 9: Gestión de servicios y troubleshooting de boot (1.227)

## systemd
* Introducción a _systemd_: https://www.certdepot.net/rhel7-get-started-systemd/

* Se encarga de:
    * Servicios.
    * Arranque: El proceso 1 es _systemd_ (antes era _init_).
    * Controla los puertos.

* _Systemd_ lleva ya unos cuantos años pero se ha implantado en las distribuciones recientemente.

* _Systemd_ tienen una gran controversia porque hace las cosas distintas a Unix (45 años) y _Linux_ (20 años).

* El 95% de las distribuciones se pasaron a _systemd_.

* En _RHEL 7_ se pueden trabajar con los comandos pre-systemd, Red Hat hicieron alias de los comandos viejos a los nuevos.

* El estándar _Unix_ establece que en _/etc/_ están los archivos de configuración. En systemd todos los archivos de configuración son binarios.

* Systemd aporta:
    * Ayuda a configuración. Por ejemplo, configurar _Apache_ (obviando _SELinux_) en una IP distinta y puerto distinto:
        * _Apache_ se lo comunicaría a systemd.
        * _systemd_ hablaría con _Networmanager_ y con el firewall.
        * _systemd_ se lo informa a _Apache_ y ya estaría la configuración realizada.
    * Mantenimiento de sockets abiertos. Por ejemplo _Apache_:
        * Se puede hacer un "systemct restart httpd" y el socket sigue abierto por lo que no se pierde el servicio en ningún momento.
    * Paraleliza de forma agresiva: El inicio del sistema es mucho más rápido ya que lo hace en paralelo.
    * En RHEL 5 y 6 había _xinetd_ del cual dependían servicios que no tenían uso habitual en el sistema (_telnet_, _FTP_...). Por ejemplo, había un _FTP_ que se usaba una vez al mes, por lo que no compensaba tener un servicio corriendo seguido, _xinetd_ esperaba a que hubiese una petición para levantar el servicio y tras un tiempo sin uso, bajaría el servicio. En RHEL 7 (graicas a systemd) se puede hacer esto por defecto con todos servicios.

* Los logs de systemd (no los de los servicios) están en binario (en memoria, aunque se puede configurar para que vayan a un directorio para que sea persistente). _RHEL 7_ reenvía estos logs al sistema antiguo (/var/log/syslog).

* _cron_ ha sido sustituido también por _systemd_. Aunque también sigue funcionando con a modo de compatibilidad.

* systemctl tiene gestión de dependencias automática.

### Gestión de servicios con systemd 

#### Unidades systemd

* Los servicios pueden tener varias unidades (son tipos de objetos de _systemd_):
	* sshd.service -> es una unidad
	* sshd.XXXXX -> otra unidad
	* la unidad .service es la por defecto. Si no se se indica otra, será esta.

* Listar todas las unidades que hay. Algunas dependen unas de otras.
> \# systemctl list-units

* Listar tipos de unidades:
> \# systemctl -t help
    * _service_
    * _socket_
    * _busname_
    * _target_
    * _snapshot_
    * _device_
    * _mount_, _automount_: Des/montaje bajo demanda.
    * _swap_: Gestión de swap.
    * _timer_: Equivalente a cron.
    * _path_: Monitorización archivo o directorio. Por ejemplo, _cups_ monitoriza un directorio de modo que si aparece en él un fichero y lo imprime.
    * slice
    * scope

* Listar unidades de un tipo:
> \# list-units --type=path

* Listar unidades que han fallado en el arranque:
> \# systemctl --failed

##### Gestión de unidades con systemd

* Mostrar estado de una unidad:
> \# systemctl status sshd
	* "service sshd status" sigue funcionando, al ejecutarlo indica que redirige a "systemctl status sshd.service"

    * Posibles estados de una unidad:
        * _loaded_: El archivo de configuración de la unidad ha sido procesado.
        * _active (running)_: Ejecutándose uno o más procesos.
        * _active (exited)_: Completada satisfactoriamente una configuración.
    	* _active (waiting)_: Ejecutándose pero esperando por un evento.
    	* _inactive_: No se está ejecutando.
    	* _enabled_: Se ejecutará al inicio del sistema.
    	* _disabled_: No se ejecutará al inicio del sistema.
    	* _static_: No se puede ejecutar al inicio del sistema pero sí puede ser invocado.

* Consultar si una unidad está activa (útil por ej. para scripts):
> \# systemctl is-active sshd
	> active

* Consultar si una unidad está _enabled_ (útil por ej. para scripts):
> \# systemctl is-enabled sshd
	> enabled

* Iniciar unidad:
> \# systemctl start sshd

* Parar unidad:
> \# systemctl stop sshd

* Reiniciar unidad:
> \# systemctl restart sshd

* Recargar configuración en caliente de unidad:
> \# systemctl reload sshd

* Iniciar unidad al arranque del sistema (como el _chkconfig_ en _RHEL 6_):
> \# systemctl enable sshd

* Deshabilitar unidad al arranque del sistema:
> \# systemctl disable sshd

* Enmascarar para que nadie pueda levantar una unidad aunque se solicite (lo que hace es hacer enlace simbólico a _NULL_):
> \# systemctl mask bluetooth.service
	> ln -s '/dev/null' '/etc/systemd/system/bluetooth.service'
	* Por ejemplo, útil para enmascarar el servicio _network_ (sistema antiguo) el cual es incompatible con _network-manager_ (nuevo sistema integrado con _systemd_).

* Mostrar unidades que son requeridas y necesarias por una unidad:
> \# systemctl list-dependencies sshd

* Mostrar como dependen unos servicios de otros:
> \# systemd-analyze critical-chain
	* @cuanto_se_tardó_en_llegar +tiempo_que_tardó_en_lebantar

### Targets

Un _target_ de _systemd_ es un conjunto de unidades que deben ser activadas para conseguir el estado deseado del sistema (lo que en _RHEL 6_ eran los _runlevels_).

* Listar targets:
> $ systemctl list-dependencies | grep target
	* Los que aparecen como hijos son dependencias del _target_ padre y se inician en paralelo.

* Para mantener compatibilidad hacia atrás, es posible usar _init_ "init 3" ya que hay alias.

* Targets más usados:
    * graphical: Equivalente a _runlevel 5_.
    * multiuser: Equivalente a _runlevel 3_, o sea, igual que gráfico, pero solo texto.
    * rescue.target: Equivalente a _runlevel 1_ (single user), aunque ahora, ahora pide password.
    * emergency.target: Es como el _runlevel 1_ pero ejecuta menos cosas todavía, se accede como _root_, pide password también.
    
* Archivos de configuración de los _targets_ se encuentran en _/usr/lib/systemd/system/_
	* Por ej: _/usr/lib/systemd/system/graphical.target_

* Mostrar dependencias de un target:
> \# systemctl list-dependencies graphical.target | grep target

* Cambiar de target en tiempo de ejecución:
> \# sysmtemcl isolate multi-user.target
	* Si se pone isolate se cambia de _target_ y tira abajo los servicios que no sean de este nuevo _target_, sin él no tiraría abajo los servicios que ya estuvieran funcionando.
	* No todos los _targets_ pueden ser usados con _isolate_. Esto es configurable en el archivo de configuración del _target_ en concreto.

* Obtener _target_ por defecto en el momento del arranque:
> \# systemctl get-default

* Establecer el target por defecto en el momento del arranque (es persistente):
> \# systemctl set-default graphical.target

* En _grub_ se puede cambiar para que inicie en el target que nos interese: Añadiendo "systemd.unit=" a la línea del kernel. Por ejemplo:
> systemd.unit=rescue.target

## Proceso de arranque de RHEL (1.239)

Tareas implicadas en el inicio:

1. Se enciende la máquina. El firmware (_BIOS_ o _UEFI_) hace un _Power On Self Test_ (POST) y se inicializa parte del hardware.

* El firmware busca un dispositivo arrancable configurado en UEFI o buscando en el MBR de todos los discos.

* El firmware lee el _boot loader_ (_grub2_) desde el disco y le pasa el control.

* _grub2_ carga su configuración desde el disco y presenta el menú de inicio al usuario.

* Tras la selección del usuario, _grub2_ carga el kernel configurado y el _initramfs_ desde el disco y los almacena en RAM.

* _grub2_ da el control del sistema al kernel pasándole las opciones configuradas y la dirección de _initramfs_ en RAM.

* El _kernel_ inicializa el hardware con los drivers que están en _initramfs_. Entonces ejecuta _/sbin/init_ desde _initramfs_ como _PID 1_

* La instancia de _systemd_ que está en _initramfs ejecuta las unidades (o sea los servicios) para el _target_ _initrd.target_. Lo cual incluye montar el raíz del sistema en _/sysroot_.

* El _kernel_ pivota desde el sistema de archivos de _initramfs_ al sistema de archivos montado en _/sysroot_. _systemd_ se ejecuta a si mismo usando una copia de _systemd_ instalada en el sistema.

* _systemd_ busca un _target_ por defecto, tanto pasado en forma de línea de omando del kernel o configurado en el sistema. Entonces inicia (y para) unidades (o sea servicios) de acuerdo con la configuración del _target_.

### Comandos inicio y apagado

* Apagar sistema:
> \# systemctl poweroff

* Reiniciar sistema:
> \# systemctl reboot

* Parar el sistema pero no se apaga físicamente el hardware:
> \# halt

### Tiempos de inicio

* Mostrar cuanto tardó en arrancar la máquina:
> \# systemd-analyze

* Mostrar cuanto tarda cada servicio al iniciar la máquina:
> \# systemd-analyze blame

* Mostrar tiempos de arranque en modo gráfico:
> \# systemd-analyze plot > arranque.svg
> \# firefox arranque.svg

## Troubleshooting
### Re-establecer password de root (1.245)

Proceso de establecimiento de password de root:

1. Reiniciar el sistema.

* Editar la entrada de _grub_ que nos interese pulsando **e**.

* Localizar la línea que empieza por _linux16_.

* Al final de la línea añadir " rd.break".

* Pulsar la combinación "ctrl+x" para iniciar.

* Obtendremos una consola como _root_ (todo archivo que se edite perderá el contexto de SELinux).

* Re-montar /sysroot como lectura y escritura:
> \# mount -oremoount,rw /sysroot

* Cambiar el raíz a _/sysroot_;
> \# chroot /sysroot

* Cambiar el password de _root_:
> \# passwd root

* Se entra sin _SELinux_ por lo que al tocar algún archivo se quedará sin contexto, entonces al rearrancar con selinux va a dar problemas. Para solucionarlo se ha de crear un archivo (como alternativa se podría forzar que entre en modo permisivo en _grub_ y hacer _restorecon_ de "shadow") para que _SELinux_ reaplique los contextos:
> \# touch /.autorelabel

* Salir del _chroot_ y de la _shell_ de _initramfs_:
> \# exit
> \# exit

* En este punto el sistema continuará iniciando, hará el _relabel_ de SELinux y reinicará normalmente.

### Consulta de logs con Journalctl (1.246)

* Hacer persistentes los logs:

> \# mkdir -p -m2775 /var/log/journal

> \# chown :systemd-journal /var/log/journal

> \# killall -USR1 systemd-journald

* Mostrar logs de arranque actual:
> \# journalctl

* Mostrar logs de arranques anteriores al actual de tipo error o peor:
> \# journalctl -b-1 -p err


### Troubleshooting de /etc/fstab (1.251)

* Montar todos las particiones de fstab
> \# mount -a

* Montar todos los swaps de fstab
> \# swapon -a

Reparar problema de _fstab_ modificando _grub_:

1. Editar línea de grub: **e**.
* Ir al final de la línea que empieza por "linux16", añadir: systemd.unit=emergency.target
* Continuar con el arranque: "ctrl+x".
* Dar password de _root_.
* Remontar _/_ como lectura y escritura:
> \# mount -oremount,rw /
* Editar _fstab_ y reiniciar.

### Troubleshooting de gestor de arranque Grub (1.254)

* Archivo de configuración principal de _grub_: "/boot/grub2/grub.cfg"

* En grub2 no se puede editar a mano "/boot/grub2/grub.cfg" ya que se regenera por si mismo a raíz de los archivos de configuración de _grub de_ _/etc/_.

* Las entradas de inicio están dentro de los bloques _menuentry_. En estos bloques, las líneas _linux16_ y _initrd16_ apuntan al kernel que se carga desde el disco y al _initramfs_ que se cargará.

* Las líneas _set root_ no apuntan al sistema de archivos raíz, en su lugar apuntan al sistema de archivos desde el que _grub2_ debe leer los archivos _kernel_ y _initramfs_. 

* _grub2-mkconfig_ genera la configuración a raíz de _/etc/default/grub_ y _/etc/grub.d/_. Para hacer persistentes los cambios que se realicen en ellos:
> \# grub2-mkconfig > /boot/grub2/grub.cfg

* En caso de que _grub_ se corrompiese, puede ser reinstalado:
> \# grub2-install 
    * Si no se le pasan argumentos, será instalado en _/boot/efi_, en caso de usar _EFI_. Si se usa _MBR_ habría que pasarle como argumento el disco donde _grub2_ se instalará en su _MBR_.

<center>2016-04-12 (martes)</center>

# Gestión de software con yum (1.172)

## Obtención de información sobre paquetes

* Buscar paquetes en los repositorios instalados:
> \# yum search bluetooth
	* Paquetes rpm cuyo nombre o descripción coincidan.

* Listar paquetes que se llamen exactamente "bluetooth".
> \# yum list bluetooth
	* Permite comodines "*" antes o detrás del paquete a buscar.
	* Muestra separado los paquetes instalados y los que están disponible spara instalar.

* Obtener información de un paquete:
> \# yum info bluez

* Ver los archivos de un paquete una vez instalado:
> \# rpm -ql bluez

* Consultar qué paquete provee un archivo esté o no instalado:
> \# yum provides /usr/bin/ls
> \# yum provides *ls

* Verificar un paquete. Cruza la base de datos del paquete con lo que hay en el disco:
\# rpm -V coreuitils

	* Ejemplo:
		> \# whereis ls
		> \# rm /usr/bin/ls
		> \# yum provides /usr/bin/ls
		> \# yum reinstall coreutils

* Listar paquetes por orden de instalación:
> \# rpm -qa --last | head

## Gestión de paquetes

* Instalar un paquete:
> \# yum install NOMBE_PAQUETE

* Desinstalar un paquete:
> \# yum remove NOMBE_PAQUETE

* Reinstalar un paquete:
> \# yum reinstall NOMBE_PAQUETE

* Cuando se instala o actualiza un paquete se machacan todos los archivos menos los de configuración.

* Herramienta gráfica de gestión de paquetes: _packagekit_

* Los paquetes descargados con yum se quedan almacenados en:
	* _/var/cache/yum/x86_64/7Server/_

* El paquete de kernel es especial, ya que permite tener varias versiones del paquete a la vez:
> \# yum list kernel

TO-DO: Revisar ortografía desde aquí para abajo

### Histórico de paquetes

* Histórico de transacciones con yum:
> \# yum history

* Instalar paquete rpm descargado en local:
> \# yum install _/directorio_local/paquete_local_

* Ejemplo deshacer transacción de yum:

	> \# yum install gnuplot (instala también gnuplot-common como dependencia)
	> \# yum history
	> \# yum history info 15
	> \# yum remove gnuplot (no desistala las dependencias)
	> \# yum history undo 15 (deshace una transacción de history)

### Grupos de paquetes

* Obtener información de un grupo:
> \# yum group info "Virtualization PLatform"

* Los paquetes vienen agrupados en grupos. Facilitando así instalaciones:
> \# yum group list

* Instalar un grupo:
> \# yum groupinstall "Virtualization Platform"

### Repositorios (1.183)

* Los archivos de repositorios están en _/etc/yum.repos.de/_. Como mínimo ha de incluir:
	* [name] (Nombre entre corchetes sin espacios en blanco).
	* baseurl=
	* gpgcheck=0 (en el examen se ha de poner a 0 ya que habrá paquetes sin firmar.)

* Un archivo de repositorio ha de acabar en _*.repo_.

* Para eliminar un repositorio basta con eliminar el archivo _*.repo_*.

* Listar repositorios instalados:
> \# yum repolist


#### Repositorio EPEL de Fedora

* **E**xtra **P**ackage for **E**nterprise **L**inux.

* Son paquetes de _Fedora_ para _RHEL_.

* _EPEL_ no invalida el soporte de _RHEL_.

* El archivo del repositorio es: _/etc/yum.repos.de/epel.repo_

# Capítulo 10: Configuración de red (1.262)

* EN _RHEL_ _ifconfig_, netstat route están como obsoletos desde _RHEL 6_, en _RHEL 7_ están disponibles para instalar, pero no están instalados por defecto en el sistema.

* En _RHEL 6_ se usaba _network_ como servicio de gestión de red, en _RHEL 7_ se usa _NetworkManager_, el cual está integrado con _systemd_. Por lo que es buena práctica deshabilitar y enmascara _network_.

## Obtener información de direccionamiento de red

* El comando _ip_ sustituye a _ifconfig_.

* Mostrar interfaces de red:
> \# ip addr
> \# ip a

* Mostrar tabla de rutas:
> \# ip route
> \# ip r

## Troubleshooting de red

* Ver si una conexión tiene link y sus estadísticas:
> \# ip -s link show eth0

* Sustituto de traceroute:
> \# tracepath access.redhat.com

* _tracepath_ + _ping_:
> \# mtr access.redhat.com

* _netstat_ es sustituído por _ss_ (1.263):
> \# ss - ta (conexiones a la escucha)

## Configuración de hostnames

* Cambiar hostname de la máquina:
> \# hostnamectl set-hostname server0.example.com
	* Se crear el archivo _/etc/hostname_

* Configuración de DNS en _/etc/resolv.conf_:
	* No se debe editar a mano, si no que hay que configurar DNS con NetworkManager

* Archivo asignación estática de IP-DNS. Con estas entradas no se preguntaría al DNS para resolver:
> /etc/hosts

* Testear resolución de nombres de _/etc/hosts_:
> $ getent hosts server0

## Comandos NetworkManager (1.267)

* Herramienta modo texto de gestión de red de _NetworkManager_:
> \# nmcli

* Interfaz curses de NetworkManager:
> \# mtui

* Interfaz gui de NetworkManager:
> \# nm-connection-editor

* Mostrar conexiones:
> \# nmcli con show

* Información de una conexión en concreto:
> \# nmcli con show "System 0"

* Listar dispositivos (cablea, wifi, bluetooth...):
> \# nmcli dev status

## Edición a mano de archivos de configuración (1.275)

* Documentación en _/usr/share/doc/initscripts-9.49.30/sysconfig.txt_

* Los archivos de configuración de red están en:

> /etc/sysconfig/network-scripts/ifcfg-*
	* No hacer "cp ifcfg-eth0 ifcfg-eth0.copia" en el dir "network-scripts" ya que _NetworkManager_ lo trataría como si fuese una nueva interfaz. Se podría hacer si el archivo no empieza por _ifcfg*_.

* Tras editar este archivo habría que recargar la configuración en NetworkManager:

	> \# nmcli con reload
	> \# nmcli con down "System eth0"
	> \# nmcli con up "System eth0"
