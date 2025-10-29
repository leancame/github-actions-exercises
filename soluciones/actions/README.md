# Actions - Ejercicios

# Ejercicio 1
## Crear una action personalizada que sea capaz de crear un archivo de texto en el directorio raíz del repositorio con un mensaje

```yml
name: "Crear archivo de texto"  # Nombre de la acción personalizada
description: "Crea un archivo de texto en el directorio raíz del repositorio con un mensaje especificado"  # Descripción de lo que hace la acción

inputs:  # Definición de los parámetros de entrada para la acción
  filename:  # Primer parámetro: nombre del archivo
    description: "El nombre del archivo a crear"  # Descripción del parámetro para los usuarios
    required: true  # Indica que este parámetro es obligatorio
    default: "archivo.txt"  # Valor predeterminado si el usuario no especifica uno
  content:  # Segundo parámetro: contenido del archivo
    description: "El mensaje que se escribirá en el archivo"  # Descripción del parámetro para los usuarios
    required: true  # Indica que este parámetro es obligatorio
    default: "Hola, mundo!"  # Valor predeterminado si el usuario no especifica uno

runs:  # Especifica cómo se ejecutará esta acción
  using: "composite"  # Tipo de acción personalizada que utiliza directamente scripts en lugar de contenedores o binarios
  steps:  # Lista los pasos que se ejecutarán en la acción
    - shell: bash  # Indica que el script se ejecutará en un entorno Bash
      run: |  # Comando multi-línea para ejecutar
        echo "${{ inputs.content }}" > "${{ inputs.filename }}"
```

La action del ejercicio 1 crea un archivo de texto en el directorio raíz del repositorio con el contenido especificado. Los parámetros de entrada permiten personalizar el nombre del archivo y el contenido que se escribirá en el archivo. Para los parámetros de entrada, se utiliza el formato de entrada de la siguiente manera: `inputs.<parameter_name>`. Por ejemplo, para el parámetro `filename` se utiliza `inputs.filename`. Posteriormente, se utiliza `runs` para especificar como se ejecutará la action, en este caso utilizando directamente scripts con `using: "composite"`. El script utiliza el comando `echo` para escribir el contenido en el archivo. 

Workflow para usar esta action:

```yml
name: Test Crear Archivo  # Nombre del flujo de trabajo para identificar su propósito

on:  # Define el evento que activará este flujo de trabajo
  push:  # El flujo se activará con un push al repositorio
    branches:  # Limita la activación solo a los pushes en las ramas especificadas
      - ejercicios  # Se ejecutará cuando haya un push en la rama 'ejercicios'

jobs:  # Define los trabajos que se ejecutarán en este flujo
  test-action:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo

    steps:  # Lista los pasos que se ejecutarán en el trabajo
      - name: Checkout code  # Nombre del paso
        uses: actions/checkout@v3  # Usa la acción oficial para clonar el código del repositorio en el runner

      - name: Run custom action  # Nombre del paso
        uses: ./.github/actions/action_1  # Especifica la acción personalizada ubicada en `.github/actions/action_1`
        with:  # Define los parámetros de entrada para la acción
          filename: "saludo.txt"  # Nombre del archivo que creará la acción
          content: "Este es un mensaje de prueba desde la acción personalizada"  # Contenido del archivo

      - name: Verify file creation  # Nombre del paso para verificar la creación del archivo
        run: |  # Comando multi-línea para ejecutar
          if [ ! -f saludo.txt ]; then # Comprueba si el archivo `saludo.txt` no existe
            echo "Archivo no encontrado"  # Imprime un mensaje de error si el archivo no se encuentra
            exit 1  # Finaliza el trabajo con un error
          fi
          echo "Archivo creado correctamente"  # Imprime un mensaje indicando que el archivo se creó exitosamente
```
El workflow que usa la action del ejercicio 1 verifica la creación del archivo `saludo.txt` y muestra un mensaje indicando si se creo correctamente. 

### Ejecución

![Captura sobre el código](../../datos/actions/ejercicio%201/actions%201%20parte%201.png)

![Captura sobre el código](../../datos/actions/ejercicio%201/actions%201%20parte%202.png)

![Captura sobre el código](../../datos/actions/ejercicio%201/actions%201%20parte%203.png)


# Ejercicio 2
## Crear una custom action usando composite actions que imprime el nombre del autor del último commit en los logs del workflow

```yml
name: "Obtener autor del último commit"  # Nombre de la acción
description: "Obtiene el nombre del autor del último commit y lo registra en los logs"  # Descripción de lo que hace la acción

outputs:  # Define las salidas de la acción
  author:  # Nombre de la salida
    description: "Nombre del autor del último commit"  # Descripción de la salida para los usuarios

runs:  # Especifica cómo se ejecutará esta acción
  using: "composite"  # Tipo de acción personalizada que utiliza scripts directamente
  steps:  # Lista los pasos que se ejecutarán en la acción
    - name: Obtener autor del último commit  # Nombre del paso
      id: get-author  # Identificador del paso, útil para referencias posteriores
      run: |  # Comando multi-línea para ejecutar
        author=$(git log -1 --pretty=format:'%an')  # Obtén el autor del último commit usando `git log`
        echo "Autor: $author"  # Imprime el nombre del autor en los logs para referencia
        echo "El autor del último commit es: $author"  # Mensaje adicional en los logs para claridad
      shell: bash  # Indica que el script se ejecutará en un entorno Bash
```

La action del ejercicio 2 obtiene el nombre del autor del últimos commit en los logs del workflow y lo registra en la salida `author`. La clave para poder saber del autor del último commit es el uso de `git log -1 --pretty=format:'%an'`. El script utiliza el comando `echo` para escribir el nombre del autor en los logs. 

Workflow para usar esta action:

```yml
name: Test Obtener Autor del Último Commit  # Nombre del flujo de trabajo para identificar su propósito

on:  # Define el evento que activará este flujo de trabajo
  push:  # El flujo se activará con un push al repositorio
    branches:  # Limita la activación solo a los pushes en las ramas especificadas
      - ejercicios  # Se ejecutará cuando haya un push en la rama 'ejercicios'

jobs:  # Define los trabajos que se ejecutarán en este flujo
  test-action:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo

    steps:  # Lista los pasos que se ejecutarán en el trabajo
      - name: Checkout code  # Nombre del paso
        uses: actions/checkout@v3  # Usa la acción oficial para clonar el código del repositorio en el runner

      - name: Run custom action  # Nombre del paso para ejecutar la acción personalizada
        uses: ./.github/actions/action_2  # Ruta a la acción personalizada dentro del repositorio
        id: get-author  # Identificador del paso, necesario para referenciar sus outputs
```

Este workflow utiliza la action del ejercicio 2 para obtener el autor del último commit y registrarlo en la salida `author` de la propia action

### Ejecución

![Captura sobre el código](../../datos/actions/ejercicio%202/action%202%20parte%201.png)

![Captura sobre el código](../../datos/actions/ejercicio%202/action%202%20parte%202.png)

![Captura sobre el código](../../datos/actions/ejercicio%202/action%202%20parte%203.png)

![Captura sobre el código](../../datos/actions/ejercicio%202/action%202%20parte%204.png)


# Ejercicio 3
## Crear un workflow que suba un archivo existente en el repositorio como un artefacto, y luego lo descargue y muestre su contenido en una tarea posterior

```yml
name: Subida y Descarga de Archivos  # Nombre del flujo de trabajo para identificar su propósito

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Permite ejecutar el flujo de trabajo manualmente desde la interfaz de GitHub

permissions:  # Define los permisos requeridos para este flujo de trabajo
  contents: read  # Permite leer contenido del repositorio

jobs:  # Define los trabajos que se ejecutarán en este flujo
  upload-artifact:  # Primer trabajo: "upload-artifact"
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en este trabajo
      - name: Checkout del repositorio  # Nombre del paso
        uses: actions/checkout@v3  # Usa la acción oficial para clonar el código del repositorio en el runner

      - name: Mostrar contenido de file.txt antes de subir  # Nombre del paso
        run: |  # Comando multi-línea para ejecutar
          echo "Contenido original de file.txt:"  # Mensaje descriptivo
          cat file.txt  # Muestra el contenido del archivo `file.txt` en los logs

      - name: Subir file.txt como artefacto  # Nombre del paso
        uses: actions/upload-artifact@v4  # Usa la acción oficial para subir un artefacto
        with:  # Parámetros para la acción
          name: artefacto_file  # Nombre del artefacto que se subirá
          path: file.txt  # Ruta del archivo que se subirá como artefacto

  download-and-print:  # Segundo trabajo: "download-and-print"
    needs: upload-artifact  # Define que este trabajo depende del trabajo "upload-artifact"
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en este trabajo
      - name: Descargar el artefacto  # Nombre del paso
        uses: actions/download-artifact@v4  # Usa la acción oficial para descargar un artefacto
        with:  # Parámetros para la acción
          name: artefacto_file  # Nombre del artefacto que se descargará (debe coincidir con el nombre usado al subirlo)
          path: downloaded-files/  # Carpeta donde se guardará el artefacto descargado

      - name: Mostrar contenido descargado  # Nombre del paso
        run: |  # Comando multi-línea para ejecutar
          echo "Contenido descargado de downloaded-files/file.txt:"  # Mensaje descriptivo
          cat downloaded-files/file.txt  # Muestra el contenido del archivo descargado en los logs
```
Este workflow utiliza la action `actions/upload-artifact` para subir un archivo como un artefacto, y luego la action `actions/download-artifact` para descargarlo y mostrar su contenido en una tarea posterior. Este el supuesto por antonomasia cuando queremos pasar archivos entre jobs. El nombre del artefacto es `artefacto_file` teniendo la referencia idéntica en todos los puntos del workflow ya que si no coincide no se puede descargar, y la carpeta donde se guardan los archivos es `downloaded-files`.

### Ejecución

![Captura sobre el código](../../datos/actions/ejercicio%203/action_3%20parte%201.png)

![Captura sobre el código](../../datos/actions/ejercicio%203/action_3%20parte%202.png)

![Captura sobre el código](../../datos/actions/ejercicio%203/action_3%20parte%203.png)

![Captura sobre el código](../../datos/actions/ejercicio%203/action_3%20parte%204%20solo%20upload.png)
