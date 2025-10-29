# Variables y Outputs - Ejercicios (Ejercicio 5 posee su propio README)



# Ejercicio 1
## Configura un workflow con un solo job que haga las siguientes tareas

- En el primer step, define dos variables de entorno.
- En el segundo step, usa estas variables para ejecutar un comando o script.

```yml
name: Variables - Ejercicio 1  # Nombre del workflow

on:  # Define el evento que activará este workflow
  workflow_dispatch:  # Permite ejecutar el workflow manualmente desde la interfaz de GitHub

jobs:  # Define los trabajos que se ejecutarán en este workflow
  variables:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Especifica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en este trabajo
      - name: Define variables  # Nombre del paso
        run: |  # Comando multi-línea para ejecutar
          echo "VARIABLE_1=Hola" >> $GITHUB_ENV  # Define VARIABLE_1 y la guarda en el entorno de GitHub
          echo "VARIABLE_2=soy Leandro" >> $GITHUB_ENV  # Define VARIABLE_2 y la guarda en el entorno de GitHub

      - name: Print variables  # Nombre del paso
        run: |  # Comando multi-línea para ejecutar
          echo "VARIABLE_1: $VARIABLE_1"  # Imprime el valor de VARIABLE_1
          echo "VARIABLE_2: $VARIABLE_2"  # Imprime el valor de VARIABLE_2
```

Empezamos este punto con el ejercicio 1, que consiste en configurar un workflow que define dos variables de entorno y las imprime en un paso posterior. Usaremos el entorno de GitHub (`$GITHUB_ENV`) para almacenar estas variables para que puedan ser usadas en otros pasos.

### Ejecución

![Captura sobre el código](../../datos/variables/ejercicio%201/variables%201%20parte%202.png)

![Captura sobre el código](../../datos/variables/ejercicio%201/variables%201%20parte%203.png)


# Ejercicio 2
## Configura un job que use un secreto definido en el repositorio (por ejemplo, MY_SECRET) y lo use en un comando.

```yml
name: Variables - Ejercicio 2  # Nombre del workflow

on:  # Define el evento que activará este workflow
  workflow_dispatch:  # Permite ejecutar el workflow manualmente desde la interfaz de GitHub

jobs:  # Define los trabajos que se ejecutarán en este workflow
  secreto:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Especifica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en este trabajo
      - name: Usar el secreto  # Nombre del paso
        env:  # Define las variables de entorno para este paso
          MY_SECRET: ${{ secrets.MY_SECRET }}  # Asigna el secreto MY_SECRET a una variable de entorno
        run: |  # Comando multi-línea para ejecutar
          echo "Este es mi secreto: $MY_SECRET"  # Imprime el valor del secreto
```

Antes de empezar con el ejercicio 2, configuraremos un secreto en el repositorio para que pueda ser utilizado en el job. En este caso, usaremos el nombre `MY_SECRET` para el nombre del secreto. Además, definimos las variables de entorno en el `env` difiniendo el secreto como una variable de entorno llamada `MY_SECRET`. Tras ello, usaremos la variable de entorno `MY_SECRET` en el comando para imprimir el valor del secreto. Claramente, no nos aparecerá este, sino una serie de asteriscos.

### Ejecución


![Captura sobre el código](../../datos/variables/ejercicio%202/variables%202%20parte%201.png)

![Captura sobre el código](../../datos/variables/ejercicio%202/variables%202%20parte%202.png)

![Captura sobre el código](../../datos/variables/ejercicio%202/variables%202%20parte%203.png)



# Ejercicio 3
## Configura un workflow siguiendo estos pasos:

- Define un step que genere un número aleatorio del 1 al 100.
- En el segundo step, utiliza un condicional para ejecutar comandos solo si el número es mayor a 50.


```yml
name: Variables - Ejercicio 3  # Nombre del workflow

on:  # Define el evento que activará este workflow
  push:  # El workflow se ejecuta al hacer un push
    branches:  # Específicamente en la rama 'ejercicios'
      - ejercicios

jobs:  # Define los trabajos que se ejecutarán en este workflow
  random-number-job:  # Nombre del trabajo
    runs-on: ubuntu-latest  # El trabajo se ejecutará en un runner de Ubuntu

    steps:  # Lista de pasos que se ejecutarán en este trabajo
      # Step 1: Generar un número aleatorio
      - name: Generar un número aleatorio  # Nombre del paso
        id: random_number  # Identificador del paso
        run: echo "RANDOM_NUMBER=$((RANDOM % 100 + 1))" >> $GITHUB_ENV  # Genera un número entre 1 y 100 y lo guarda en el entorno de GitHub

      # Step 2: Condicional para ejecutar si el número es mayor a 50
      - name: Condicional para ejecutar si el número es mayor a 50  # Nombre del paso
        if: ${{ env.RANDOM_NUMBER > 50 }}  # Condición: ejecuta este paso si el número aleatorio es mayor a 50
        run: |  # Comando multi-línea para ejecutar
          echo "The random number $RANDOM_NUMBER is greater than 50."  # Imprime un mensaje indicando que el número es mayor a 50
```

El segundo supuesto es el de condicionales, donde usaremos la variable de entorno `RANDOM_NUMBER` para ejecutar comandos solo si el número generado es mayor a 50. En este punto, el segundo paso se ejecutará solo si el número generado es mayor a 50. Para la condición del `if` realizaremos una comparación de la variable de entorno `RANDOM_NUMBER` con el valor 50.

### Ejecución

![Captura sobre el código](../../datos/variables/ejercicio%203/variables%203%20parte%201.png)

![Captura sobre el código](../../datos/variables/ejercicio%203/variables%203%20parte%202.png)


# Ejercicio 4

## Configura un workflow que valide un archivo de configuración (config.json) y realice un despliegue condicional a develop o producción basado en la rama desde la cual se hace el push.

### Pasos:

- Verificar que el archivo config.json exista y que tenga el formato JSON válido.
- Extraer un valor específico del archivo config.json (por ejemplo, la versión de la aplicación) y utilizarlo como un output.
- Si el push se hace en la rama main, el workflow debería simular un despliegue de la aplicación en producción.
- Si el push se hace en una rama develop, el workflow debería simular un despliegue de la aplicación en develop.
- Si el archivo config.json no es válido, el workflow debería fallar y detenerse sin realizar el despliegue.

```yml
name: Validar Config y Desplegar  # Nombre del workflow

on:  # Define los eventos que activan este workflow
  push:  # Se ejecuta al hacer un push
    branches:  # En las ramas especificadas
      - main
      - develop
      - feature

jobs:  # Define los trabajos que se ejecutarán en este workflow
  deploy:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Especifica el tipo de runner donde se ejecutará el trabajo

    steps:  # Lista de pasos para el trabajo
      # Paso 0: Clonar el repositorio
      - name: Clonar el repositorio  # Nombre del paso
        uses: actions/checkout@v4  # Acción para clonar el código del repositorio

      # Paso 1: Verificar existencia de config.json
      - name: Verificar existencia de config.json  # Nombre del paso
        run: |  # Comando multi-línea
          if [ ! -f config.json ]; then  # Comprueba si el archivo no existe
            echo "Error: config.json no existe."  # Imprime un mensaje de error
            exit 1  # Finaliza el workflow con error
          fi

      # Paso 2: Validar formato JSON del archivo config.json
      - name: Validar formato JSON  # Nombre del paso
        run: |  # Comando multi-línea
          if ! jq . config.json > /dev/null 2>&1; then  # Valida el formato JSON usando jq
            echo "Error: config.json no tiene un formato JSON válido."  # Mensaje de error si el formato es inválido
            exit 1  # Finaliza el workflow con error
          fi

      # Paso 3: Extraer la versión desde config.json y establecerla como un output
      - name: Extraer versión de la aplicación  # Nombre del paso
        id: extraer-version  # Identificador del paso
        run: |  # Comando multi-línea
          version=$(jq -r '.version' config.json)  # Extrae la versión usando jq
          if [ -z "$version" ]; then  # Comprueba si la versión no está definida
            echo "Error: No se pudo extraer la versión de config.json."  # Mensaje de error
            exit 1  # Finaliza el workflow con error
          fi
          echo "version=$version" >> "$GITHUB_OUTPUT"  # Establece la versión como un output

      # Paso 4: Usar la versión extraída en el despliegue
      - name: Mostrar la versión extraída  # Nombre del paso
        run: |  # Comando multi-línea
          echo "La versión extraída es: ${{ steps.extraer-version.outputs.version }}"  # Imprime la versión extraída

      # Paso 5: Simular despliegue basado en la rama
      - name: Simular despliegue  # Nombre del paso
        run: |  # Comando multi-línea
          if [[ "${{ github.ref_name }}" == "main" ]]; then  # Comprueba si la rama es 'main'
            echo "Realizando despliegue en Producción con versión ${{ steps.extraer-version.outputs.version }}"  # Mensaje de despliegue en producción
          elif [[ "${{ github.ref_name }}" == "develop" ]]; then  # Comprueba si la rama es 'develop'
            echo "Realizando despliegue en Desarrollo con versión ${{ steps.extraer-version.outputs.version }}"  # Mensaje de despliegue en desarrollo
          else  # Si la rama no es 'main' ni 'develop'
            echo "No se especificó una rama válida para el despliegue."  # Mensaje para rama no válida
```


El cuarto ejercicio posee varios puntos a tener en cuenta: Comenzamos clonando el repositorio con una acción personalizada `actions/checkout@v4` para poder realizar las comprobaciones posteriores. Luego, verificamos si existe el archivo `config.json` utilizando el comando `if [ ! -f config.json ]; then`y si no existe, imprimimos un mensaje de error y finalizamos el workflow con un error. Posteriormente, validamos el formato de este usando `jq` a través de otro bloque condicional `if ! jq . config.json > /dev/null 2>&1; then`. Tras esto, extraemos los datos añadiendo estos a una variable con `version=$(jq -r '.version' config.json)` y luego establecemos el output con `echo "version=$version" >> "$GITHUB_OUTPUT"` en caso de que exista. Finalmente, imprimimos la versión extraída y realizamos el despliegue basado en la rama a traves de una serie de condicionales.

### Ejecución

![Captura sobre el código](../../datos/variables/ejercicio%204/ejecucion%20main.png)

![Captura sobre el código](../../datos/variables/ejercicio%204/ejecucion%20develop.png)

El archivo config no existe:

![Captura sobre el código](../../datos/variables/ejercicio%204/fallo%20de%20config.png)



