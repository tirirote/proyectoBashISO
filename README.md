## Ejercicio 1

> [!NOTE] Enunciado del Ejercicio
> *Realiza un script llamado comprobarApache.sh, que compruebe cada minuto si el servicio apache2 está activo (running).*
> 
> *Si está parado, entonces:*
> 1. *Introduce una línea: “Error-Apache: Fecha y hora actual” en /root/ApacheError.tmp, donde FechaActual, representa día, mes, año, hora y minuto.*
> 2. *Reinicia el servicio apache2 Para comprobarlo, para el servicio. Ejecuta el script en segundo plano y observa si lo reinicia y crea el archivo.*
> 3. *Además del script, crea una tarea programada, de forma que ese script se ejecute cada 6 horas, todos los días. Y si el ordenador está apagado, se debe ejecutar la próxima vez que se inicie, transcurrido cinco minutos.*
