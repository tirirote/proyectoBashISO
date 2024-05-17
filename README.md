

## PROYECTO DE SCRIPTS DE ISO
![imagen](https://github.com/tirirote/proyectoBashISO/assets/84339698/a0536fea-0124-4c63-bf3a-dd6387754c6a)

*Grupo 3 : Antonio Cuesta Suarez, Javier Gonzalez Marín y José Francisco Aguza Benítez.*

___
## Índice

1. [Introducción](#Introducción)
2. [Contenido](#Contenido)
	1. [Ejercicio 1](#Ejercicio1)
	2. [Ejercicio 2](#Ejercicio2)
	3. [Ejercicio 3](#Ejercicio3)
	4. [Ejercicio 4](#Ejercicio4)
	5. [Ejercicio 5](#Ejercicio5)
3. [Bibliografía](#Bibliografía)


___





# Introducción

Este proyecto de bash consiste en la creación de scripts avanzados, para gestión de usuario, cuotas y servicios. También ha sido importante el uso de cron y anacron para la creación y configuración de tareas.

Este proyecto consta de 5 scripts, cada uno de los scripts contiene la siguiente rúbrica de contenido:

- Enunciado
- Planteamiento del Enunciado,
- Problemas a la hora de la Implementación.
- Solución Final.

# Contenido

En este apartado tendremos listados cada uno de los ejercicios del trabajo, cada uno con su correspondiente enunciado, junto con las dificultades encontradas, y finalmente la solución final.

___
## Ejercicio1

> 
> **Enunciado del Ejercicio**
> 
> *Realiza un script llamado comprobarApache.sh, que compruebe cada minuto si el servicio apache2 está activo (running).*
> 
> *Si está parado, entonces:*
> 1. *Introduce una línea: “Error-Apache: Fecha y hora actual” en /root/ApacheError.tmp, donde FechaActual, representa día, mes, año, hora y minuto.*
> 2. *Reinicia el servicio apache2 Para comprobarlo, para el servicio. Ejecuta el script en segundo plano y observa si lo reinicia y crea el archivo.*
> 3. *Además del script, crea una tarea programada, de forma que ese script se ejecute cada 6 horas, todos los días. Y si el ordenador está apagado, se debe ejecutar la próxima vez que se inicie, transcurrido cinco minutos.*

A la hora de realizar este ejercicio, no hemos tenido problemas comprobandos servicios de `apache`, ya que con los comandos de `systemctl`, no resulta una gran dificultad.

En lo que sí hemos tenido complicacines, ha sido con la programación de tareas con el `crontab`, no teníamos tanta soltura con la programación de tareas como con la creación de scripts como tal. Al final se ha conseguido completar esa parte del ejercicio.

**Problemas a la hora de la Implementación**
___
  
- [x] El servico `apache2` no se encontraba instalado en la máquina, y a la hora de realizar la instalación de `apache2` la red del centro daba problemas al estar restringida.
- [x] El servicio `apache2` no respondía, con lo que a la hora de ejecutar el script, daba error y no mostraba la salida correcta.
- [x] El fichero de `log` no se creaba correctamente.
- [x] La ruta establecida no era correcta.
- [x] Se creaba un fichero `log` independientemente de las condiciones del script.
- [x] Una vez terminado el script con éxito no lo redireccionamos a ningún lado, por lo tanto no se creaba ningún fichero `log`.

**Solución Final**
```
#!/bin/bash
#Autor: Javier GOonzález, Antonio Cuesta, José Francisco
#Versión: 1.0
#Fecha: ...
#Descripción: Este script se encarga de comprobar si apache2 esta funcionando 
#o no y si no está funcionando o de cualquier error a la hora de iniciarlo nos 
#redirecciona el error a /root/apacheError.tmp 

#Parámetros/Variables

#Funciones...................................................
reiniciarApache() {
    systemctl restart apache2
}

errorApache() {
    echo "Se va a imprimir este error apache2 a un archivo externo en /root/apacheError.tmp..."
    echo "Error-Apache: $(date '+%d-%m-%Y %H:%M')" >> /root/apacheError.tmp
}
#............................................................

#Bloque principal............................................
clear
if [ "$(id -u)" -eq 0 ] #Comprueba si eres root
then
    systemctl is-active --quiet apache2 2> /dev/null
    if [ $? -eq 0 ]
        then
            #Si apache2 funciona.
            echo "Apache está funcionando."
        else
            #Si apache2 no funciona.
            echo "Apache no está funcionando en estos momentos."
            sleep 1
            errorApache
            sleep 1
            echo "Reiniciando Apache..."
            sleep 1
            reiniciarApache
            echo "Terminado."
    fi
else
    echo "No eres root"
 fi

```

**Tarea Programada**
```
0 */6 * * * /home/usuario/Boletín/comprobarApache.sh >/dev/null 2>&1

@reboot sleep 5m && /home/usuario/Boletín/comprobarApache.sh >/dev/null 2>&1
```
## Ejercicio2


>
> **Enunciado del Ejercico**
> 
> *Realiza un script llamado usuariosBloqueados.sh, que nos muestre un menú:*
> 
> *1. Usuarios Bloqueados.*
> *2. Bloquear un usuario.*
> *3. Desbloquear usuario.*
> *4. Cerrar sesión usuario*
> *5. Salir*
> 
> *Cada opción del menú corresponde con una función.*
> ***UsuariosBloqueados** : nos muestra en pantalla los usuarios (uid>1000 y <2000) que tengan la cuenta bloqueada.*
> ***BloquearUsuario** : Nos pregunta el nombre de un usuario y lo bloqueamos.*
> ***DesbloquearUsuario** : Nos pregunta el nombre de un usuario y lo desbloqueamos.*
> ***CerrarSesion** : Nos pregunta el nombre de un usuario, y si el usuario lleva más de 30 minutos (1800 seg) sin actividad, se le cierra la sesión.*

Este ejercicio no ha sido tan complicado como el anterior, crear el menú es muy sencillo, las funciones tampoco tenían mayor dificultadad como tal. Sin embargo la función "cerrarSesión", ha resultado un poco difícil pero nada que una búsqueda en StackOverflow o en Google no pueda arreglar.

**Problemas a la hora de la Implementación**
___
- [x] Hubo problemas comprobando si la lista de usuarios bloqueados era la correcta, luego comprobando la letra `L (bloqueado)` , `P (activo)` lo corregimos.
- [x] Hubo dificultades para mostrar toda la lista de usuarios bloqueados pero con `awk` no hubo problemas al final.


**Solución Final**
```
#!/bin/bash
#Autor: Javier González, Antonio Cuesta, José Francisco Aguza
#Versión: 1.0
#Fecha: 17/05/2024
#Descripción: Este script se encarga de comprobar si apache2 
#esta funcionando o no y si no está funcionando o de cualquier 
#error a la hora de iniciarlo nos redirecciona el error a /root/apacheError.tmp 
#Parámetros/Variables

#Funciones...................................................
bloquearUsuario() {
    clear
    echo "Bloqueando al usuario $1..."
    sleep 1
    sudo usermod -L "$1"
}
desbloquearUsuario() {
    clear
    echo "Desbloqueando al usuario $1..."
    sleep 1
    sudo usermod -U "$1"
}
usuariosBloqueados(){
    echo "Monstrando usuarios Bloqueados..."
    sleep 0.5
    for line in $(passwd -S -a |awk '/L/{print $1}')
        do
            echo "$line"
            sleep 0.2
        done
    sleep 1

}
cerrarSesion(){

    clear
    echo "Buscando al usuario $1..."
    sleep 1
    if [ "$(comprobarTiempoUsuario "$1")" -gt 1 ]
        then
            echo "Se encontró al usuario $1..."
            sleep 0.2
            #Se cierra la sesion del usuario.
            echo "Cerrando sesión del usuario $1.."
            sleep 0.2
            pkill -9 -u "$1"

        else
            #No hace falta cerrar la sesion del usuario.
            echo "El usuario $1 todavía está activo..."
    fi
    sleep 1
}
comprobarTiempoUsuario() {
    
    for line in $(w -h | awk '{ print $1"|"$5 }')
        do
        usuario=$(echo "$line" | cut -d'|' -f 1)
        if [ "$usuario" = "$1" ]
                then
                    echo "$line"| cut -d'|' -f 2  | awk -F: '{ print ($1*60 + $2) }'
                    break
                else
                    .
        fi
    done
    
}
menu() {
    clear
    echo "---------------------------------"
    echo "---Menú de usuarios bloqueados---"
    echo "---------------------------------"
    echo "1. Usuarios Bloqueados"
    echo "2. Bloquear un Usuaario"
    echo "3. Desbloquear un Usuario"
    echo "4. Cerrar sesión usuario"
    echo "5. Salir"
}
#............................................................

#Bloque principal............................................

if [ "$(id -u)" -eq 0 ] #Comprueba si eres root
    then

        while true ; do
        menu
        read -rp "Selecciona una opción: " num
        case $num in 
            1)
                #Mostrar listado de usuarios bloq
                usuariosBloqueados
            ;;
            2)
                #Añadir usuario al listado
                read -rp "Nombre del usuario a bloquear :" user
                bloquearUsuario "$user"
            ;;
            3)
                #Eliminar usuario del listado
                read -rp "Nombre del usuario a desbloquear :" user
                desbloquearUsuario "$user"
            ;;
            4)
                #Cerrar sesión del usuario;
                read -rp "Nombre de usuario al cual cerrarle la sesión en este equipo: " user
                cerrarSesion "$user"
            ;;
            5)
                exit
            ;;
        esac 

done
    else
        echo "No eres root"
        exit
fi

```

## Ejercicio3

> 
> **Enunciado del Ejercico**
> 
> Realiza un script llamado crearBorrarUsuarios.sh, que nos muestre un menú:
> 
> 1. Crear Usuarios.
> 2. Borrar Usuarios.
> 3. Salir.
> 
> **CrearUsuarios**: Crea de forma masiva usuarios almacenados en el fichero
> /root/usuarios.csv.
> **BorrarUsuarios**: Borra de forma masiva usuarios almacenados en el fichero
> /root/usuarios.csv.
> 
> Además, queremos que esas cuentas queden inactivas el 30 de junio de 2024.
> 
> **Fichero usuarios.csv**
> 
> ```
> alumno01:alumno01.2019:Manuel:Dominguez:alumno01@gmail.com
> alumno02:alumno02.2019:Rosa:Benitez:alumno02@gmail.com
> alumno03:alumno03.2019:Margarita:Lopez:alumno032gmail.com
> ```
> 
> 
> Los campos son los siguientes:
> 
> - El campo 1 representa el nombre de usuario.
> - El campo 2 representa la contraseña.
> - El campo 3 representa el nombre.
> - El campo 4 representa su primer apellido.
> - El campo 5 representa su correo electrónico.
> 

Al igual que el ejercicio anterior, el menú no tiene complicaciones, a la hora de parsear el archivo o extraer el contenido, nada que el comando `cut` no pueda arreglar junto con un bucle `for`, para leer cada línea del fichero, luego para más comodidad, se ha declarado una variable por cada campo extraido con `cut` para mayor comodidad.

Luego para crear y borrar usuarios, no hay complicaciones, si usamos `useradd` y con `userdel` lo podemos borrar cómodamente.

**Problemas a la hora de la Implementación**
___
- [x] No parseamos correctamente los campos del csv usando `cut` luego corrigiendo los campos lo arreglamos, también a la hora de copiar el csv hubo errores con la colocación de ":".
- [x] Hubo dficultades con los comandos de usuario, como con los comandos `usermod` para poner la fecha de expiración.

**Solución Final**
```
#!/bin/bash
#Autor: Javier González, Antonio Cuesta, José Francisco Aguza Benitez
#Versión: 1.0
#Fecha: ...
#Descripción: Este script lee un fichero csv y crea cada 
#uno de los usuarios listados en el fichero.

#Parámetros/Variables

#Funciones...................................................
menu (){

    clear
    echo "1. Crear Usuarios"
    echo 2. Borrar Usuarios
    echo "3. Salir"
}
creaUsuarios(){

    for linea in $(cat usuarios.csv)
        do
            user=$(echo "$linea" | cut -d ":" -f 1)
            pass=$(echo "$linea" | cut -d ":" -f 2)
            nombre=$(echo "$linea" | cut -d ":" -f 3)
            apellido=$(echo "$linea" | cut -d ":" -f 4)
            mail=$(echo "$linea" | cut -d ":" -f 5)
            #Crear usuario
            creaCadaUsuario "$user" "$pass" "$nombre" "$apellido" "$mail"
            sleep 1
    done
}
creaCadaUsuario(){

    clear
    useradd "$1"
    if [ $? -eq 0 ]
        then
            usermod -e "2024-06-31" "$1"
            #sudo usermod -c "$3","$4","$5" "$1"
            echo "$1:$2" | chpasswd -e
            ##
            echo "Creando usuario $1"
            echo "Contraseña: $2"
            echo "Nombre: $3"
            echo "Apellido: $4"
            echo "Mail: $5"
        else
            echo "El usuario $1 no se ha podido crear" 
    fi
    
}
borraUsuarios(){

    for linea in $(cat usuarios.csv)
        do
            user=$(echo "$linea" | cut -d ":" -f 1)
            #Borrando Usuario
            clear
            sudo userdel "$user" >> /dev/null
            if [ $? -eq 0 ]
                then
                    echo "Borrando usuario $user"
                else
                    echo "El usuario $user no se ha podido borrar" 
            fi
            sleep 1
    done
}
#............................................................

#Bloque principal............................................
if [ "$(id -u)" -eq 0 ] #Comprueba si eres root
    then
    while true
        do
        menu
        read -rp "Elige una opción: " num
        case "$num" in
            1)
                #Crear Usuarios
                creaUsuarios
            ;;
            2)
                #Borar Usuarios
                borraUsuarios
            ;;
            3)
                #Salir
                exit
            ;;
            *)
                echo "Opción Incorrecta"
                sleep 2
            ;;
        esac
    
done
    else
    echo "No eres root"
    exit
fi
```

## Ejercicio4

> 
> **Enunciado del Ejercicio**
> 
> Crea en un script llamado crearUsuarios.sh que permita crear usuarios de forma
> automática. Indicaciones:
> 
> 1. Al script se le pasa dos parámetros:
> 
> 	a. El primer parámetro representa el nombre de un usuario genérico.
> 	b. El segundo parámetro representa el número de usuarios que quiere crearse.
> 
> **Ejemplo** (Creará los usuarios: alumno1, alumno2, alumno3):
> ```
> sh crearUsuarios.sh alumno 3 
> ```
> 
> 1. A cada usuario se le asigna la contraseña que coincida con el nombre de usuario.
> 2. Al usuario se le obliga a cambiar de contraseña, cuando se loguee.
> 3. Se crea un archivo: usuariosCreados-FechaActual.tmp con el nombre de los usuarios creados y la contraseña asignado, separados por “:”.
> 4. El archivo usuariosCreados-FechaActual.tmp tiene que ser mostrado en pantalla tras la ejecución del script

Este script al tomar valores en consola, es necesario comprobar si no se han dado ningún parámetro en consola, nada que `#?` no pueda arreglar, comprobando si el número de parámetros son 0, podemos a continuación pedir por consola los valores para el script con `read`.

Luego para crear usuarios de forma masiva, se podría hacer un bucle `for` sencillo que recorra una secuencia desde el 1, hasta el último valor que ha dado el usuario, sería por ejemplo:

```
for i in $(seq 1 "$num")
	do
		#Creando Usuarios
		echo "Creando usuario$i..."
done
	echo "Terminado."
```

**Problemas a la hora de la Implementación**
____
- [x] Hubo problemas con los comando para asignar la información al usuario. Al final nos docuemtamos mejor y lo solucionamos.
- [x] No se mostraba el fichero al final, simplemente para arreglarlo se implementó la función `preguntarMostrarFichero`.
- [x] El fichero no se exportaba correctamente.


**Solución Final**
___
```
#!/bin/bash
#Autor: Javier González, Antonio Cuesta, José Francisco Aguza Benitez
#Versión: 1.0
#Fecha: ...
#Descripción: Este script se encarga de crear usuarios de forma masiva
#crea un usuario con un nombre pedido en consola y crea una 
#cantidad de usuarios determinado por otro valor pedido en consola 

#Parámetros/Variables
#Funciones...................................................
creaCadaUsuario(){

    echo "Creando usuario $1$2 ..."
    if [ $? -eq 0 ]
        then
            sleep 1
            echo "Asignando contraseña a $1$2..."
            sleep 1
            fichero=usuariosCreados-"$(date +%d-%m-%Y)".txt
            echo "$1$2:$1$2" >> "$fichero"
            echo "Exportando usuario:contraseña a usuariosCreados-$(date).txt"
            sleep 1
        else
        echo "No se ha podido crear el usuario $1"
    fi
}
creaUsuarios() {

    for i in $(seq 1 $2);
        do
            useradd -e 0 "$1$i"
            if [ $? -eq 0 ]
                then
                    creaCadaUsuario "$1" "$i"
                else
                    echo "No se ha podido crear el usuario $1"
            fi


    done
        echo "Terminado."
    preguntarMostrarFichero
            
}
preguntarMostrarFichero(){
    read -rp "¿Desea mostrar el fichero usuariosCreados.txt? (s/n): " mostrar
    if [ "$mostrar" = "s" ]
        then
            cat "$fichero"
            if [ $? -eq 0 ]
            then
                echo "Terminado."
            else
                echo "No se puede mostrar el fichero..."
            fi
    
        else
            exit
    fi
}
#............................................................

#Bloque principal............................................
if [ "$(id -u)" -eq 0 ] #Comprueba si eres root
    then
        #Si no has introducido ningún parámetro  
        if [ $# -eq 0 ]
            then
                clear
                echo "No has introducido ningún valor"
                sleep 1
                clear
                read -rp "Nombre de usuario : " nombre
                read -rp "Cantidad de usuarios : " count
                creaUsuarios "$nombre" "$count"
            else
                creaUsuarios "$1" "$2"
        fi
        
    else
        echo "No eres root"
fi
```

## Ejercicio5

> 
> **Enunciado del Ejercicio**
> 
> Partimos de que tenemos varios usuarios: usuario1, usuario2, usuario3.
> 
> - Al usuario1, se le ha establecido una cuota de disco: 40k y 100K (soft y hard respectivamente).
> 
> Realiza un script llamado cuotasUsuarios.sh, que nos copie la cuota del usuario1 a todos los usuarios cuyo uid >1000 y uid<2000
> 

Para realizar este script, volvimos a el tema de las cuotas dado en clase, y a través de las presentaciones pudimos hacer la mayor parte del script, como definir las cuotas para el usuario desde el que después la redirigiremos, así como comprobar el uid de los usuarios.  

Una vez realizado creamos otro usuario con una `uid de 2001` y ejecutamos el script a ver si a este usuario “prueba” se le colocaría las cuotas y como nos esperábamos, no se le copio la cuota del usuario1 al usuario creado “prueba”

**Problemas a la hora de la Implementación**
___

- [x] Al principio no nos localiza las cuotas del usuario1. quitamos las cuotas y las volvimos a colocar.
- [x] El script no nos reconoció los usuarios con uid de 1000 a 2000 para colocarles a estos usuarios las cuotas del usuario 1. Simplemente fué un error de sintaxis


**Solución Final**
____
```
#!/bin/bash
#Autor: Javier González, Antonio Cuesta, José Francisco
#Versión: 1.0
#Fecha: ...
#Descripción: Este script realiza una copia de las cuotas del usuario1 a todos
#los usuarios cuyo uid >1000 y uid<2000

#Parámetros/Variables
soft_quota=$(sudo -u usuario1 quota -s | awk 'NR==3{print $3}')
hard_quota=$(sudo -u usuario1 quota -s | awk 'NR==3{print $4}')
#Funciones...................................................
#............................................................

#Bloque principal............................................
clear
for user in $(awk -F: '$3 >= 1000 && $3 < 2000 {print $1}' /etc/passwd);
do    
	edquota -p usuario1 $user
	echo "Cuota establecida para:  $user: soft=$soft_quota, hard=$hard_quota"
done

echo "Operación completada con éxito."
```
# Conclusión

*Con este trabajo hemos aprendido a trabajar en grupo, de una forma más o menos eficiente, lo más importante es mejorar con el tiempo. Afortunadamente bash es un lenguaje estructurado que no es excesivamente complicado ni tiene una cantidad de "boilerplate" exagerada, con lo que la curva de aprendizaje es muy asequible para principiantes.*

*Este proyecto no ha sido extremanadamente complicado, pero si que es cierto que ha coincidido con el 3er Trimestre del curso y todas las demás asignaturas han bombardeado a la clase con trabajos y proyectos, así que no le hemos podido dedicar al proyecto todo el tiempo que hubiéramos querido.*

*Hemos intentado documentar correctamente el código y optimizarlo correctamente sin introducir redundancia en los propios scripts.*

*A la hora de usar Git Hub, no hemos tenido problemas como tal, ya que únicamente se pedía este documento y subir los scripts comprimidos, pero era un extra añadido que ha añadido algo de complicaciones al proyecto.*

# Bibliografía

https://stackoverflow.com/questions/tagged/bash
https://www.google.com/?hl=es
https://www.enmimaquinafunciona.com/pregunta/116999/como-puedo-listar-todos-los-usuarios-bloqueados-en-linux
https://laguialinux.es/comandos-linux-para-apagar-reiniciar-o-cerrar-sesion/
https://access.redhat.com/documentation/es-es/red_hat_enterprise_linux/8/html/managing_file_systems/assigning-quotas-per-user_configuring-disk-quotas
https://www.linuxtotal.com.mx/index.php?cont=info_admon_018
https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-20-04-esP
