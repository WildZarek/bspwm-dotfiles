Para crear la función **`settarget`**, necesitas crear en la **`.bashrc`** o en la **`.zshrc`** (dependiendo del tipo de Shell que uses) la siguiente función:

## .bashrc / .zshrc

```bash
function settarget(){
    ip_address=$1
    machine_name=$2
    echo "$ip_address $machine_name" > $HOME/.config/bin/htbtarget
}
```

En caso de que los directorios listados anteriormente no existan, los creas, ¿estamos?, ni más... ni menos.
El archivo de texto residente en **`/home/tuUsuario/.config/bin/htbtarget`** no es más que el contenido a representar.
Si te fijas en el uso de la función, lee 2 parámetros, el primero de ellos correspondiente a la dirección IP y el segundo de ellos al nombre de la máquina. 

Su ejecución sería tal que así:
> settarget 10.10.10.10 nombreMaquina

Es necesario proporcionarle 2 argumentos siguiendo la lógica de la función para que funcione. 

Esto por un lado, pero no es suficiente, dado que para que te salga arriba a la derecha en la Polybar (o donde lo quieras representar),
es necesario crear un nuevo módulo. Para ello, te vas a ir a la ruta **`/home/tuUsuario/.config/polybar`** y vas a abrir el archivo **`current.ini`**.

## current.ini

Una vez abierto, verás que existen varias líneas empezando con la cadena **`[bar/...]`**
En este punto, crearemos un nuevo bar siguiendo la misma estructura, correspondiente al componente que nos permitirá cargar un nuevo módulo.
Lo haremos tal que así:

```
[module/target_to_hack]
type = custom/script
interval = 2
exec = ~/.config/bin/hackthebox_target.sh
```

De esta forma, con una tasa de refresco de 2 segundos conseguiremos que el script situado en **`~/.config/bin/hackthebox_target.sh`** se ejecute.
¿El qué cómo dices?, ¿que no existe?, eso no me lo dices a la cara. Pues lo que hacemos es crearlo.

## hackthebox_target.sh

Bajo la ruta citada, crearemos el archivo **`hackthebox_target.sh`** (o como lo quieras llamar, pero debe coincidir con lo indicado en el archivo **current.ini**).
Este archivo, deberá poseer permisos de ejecución, y debe disponer del siguiente contenido:

```bash
#!/bin/bash
# Modified by WildZarek

ip_address=$(cat $HOME/.config/polybar/bin/htbtarget | awk '{print $1}')
machine_name=$(cat $HOME/.config/polybar/bin/htbtarget | awk '{print $2}')

if [ $ip_address ] && [ $machine_name ]; then
    status=$(ping -c 1 -W 1 $ip_address | grep received | awk '{print $4}')
    if [ $status -eq 0 ]
    then
        echo "%{T3}%{F#e51d0b}%{u-}%{T6} $ip_address%{u-} - $machine_name"
        export
    else
        echo "%{T3}%{F#1bbf3e}%{u-}%{T6} $ip_address%{u-} - $machine_name"
        export
    fi
else
    echo "%{T3}%{F#e51d0b}%{u-}%{T6} No target found!"
fi
```

Para que finalmente aparezca, sólo queda retocar el archivo **`launch.sh`** existente bajo la ruta **`/home/tuUsuario/.config/polybar`** y añadir la siguiente línea:
 
> polybar micomponente -c ~/.config/polybar/current.ini &
 
Donde 'micomponente' debe coincidir con el nombre definido en el bar creado en el archivo 'current.ini' (es decir, en la línea donde definimos '[bar/micomponente]').
 
¡Y listo!, de esta forma, tendrás tu nuevo módulo configurado.
Cabe decir que igual a nivel de proporciones tengas que aplicar algunos ajustes, porque tal vez las proporciones que yo tenga no son las mismas que las tuyas.
En ese caso, es simplemente ir ajustando hasta tener el módulo de la polybar en la posición deseada.