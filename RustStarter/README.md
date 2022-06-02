# Rust Starter

Esta plantilla es un fork de https://github.com/Learn-NEAR-Hispano/NCD-samples

## Contenido

Este repositorio contiene un contrato inteligente con los siguientes métodos:
### Escritura:
* `set_registrar_perrito`
* `set_adoptado`
### Lectura:
* `get_perrito`
* `get_perritos`

El contrato se encuentra previamente desplegado en la cuenta `dev-1654151703614-97110545887614`. Puedes hacer llamadas al mismo de la siguiente manera:

```sh
near view dev-1654151703614-97110545887614 get_perritos
```

## Uso

### Compilando y desplegando

Se incluye el script `build.sh`. Para hacer funcionar tu contrato es necesario ejecutar el siguiente comando en la consola:

```sh
./build.sh
```

Esto instalará las dependencias necesarias, compilará el proyecto y desplegará el contrato en una cuenta neardev.

### Usando variables de entorno

Una vez compilado y desplegado tu proyecto, vamos a requerir identificar la cuenta neardev. Esta la puedes encontrar en el archivo `Rust/neardev/neardev`. Podemos almacenar este contrato en una variable de entorno ejecutando lo siguiente en la consola, y sustituyendo por tu cuenta de desarrollo:

```sh
export CONTRATO=dev-1654151703614-97110545887614
```

Haciendo esto, podemos comprobar que la variable `CONTRATO` tiene almacenada nuestra cuenta dev.

```sh
echo $CONTRATO
```

### Métodos

Lo primero que debemos hacer es registrar al menos un usuario en el contrato. Para esto utilizamos el método `set_registrar_perrito`. Este método requiere que se pague 1 NEAR para poder ser ejecutado. El método registra a la persona que lo está ejecutando como participante.

```sh
near call $CONTRATO set_registrar_perrito '{"cuenta": "wallet perrito", "nombre":"Nombre Perrito","refugio": "Refugio asignado", "meta": "cantidad meta"}' --accountId tucuenta.testnet --amount 1
```

Ahora que tenemos al menos 1 perrito registrado, podemos utilizar los métodos de lectura. `get_perrito` nos traerá la información específica de un participante dependiendo la cuenta que le enviemos como parámetro. Por otro lado, `get_perritos` nos trae la lista de todos los participantes registrados.

```sh
near view $CONTRATO get_perrito '{"cuenta":"cuenta.testnet"}'
```

```sh
near view $CONTRATO get_perritos
```

Por último, si queremos marcar como adoptado a uno de los perritos registrados, podemos hacer uso del método `set_adoptado`. Este método tiene una restricción en la que, si tu cuenta no es la especificamente definida, no te permitirá ejecutarlo. Esta es una forma de agregar una restricción a cuentas específicas. Puedes modificar esta cuenta en el código del contrato. 

#Además, el método transfiere una compensación de 5 NEAR al participante por haber logrado su certificación.

```sh
near call $CONTRATO set_adoptado '{"cuenta":"cuenta.testnet"}' --accountId cuenta.testnet
```
