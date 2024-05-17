## Ejercicio 2


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


**CÓDIGO FINAL**
```
#!/bin/bash
#Autor: Javier González, Antonio Cuesta, José Francisco Aguza
#Versión: 1.0
#Fecha: 17/05/2024
#Descripción: Este script se encarga de comprobar si apache2 
#esta funcionando o no y si no está funcionando o de cualquier 
#error a la hora de iniciarlo nos redirecciona el error a /root/apacheError.tmp 
#Parámetros/Variables

#Funciones
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
#Bloque principal
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
