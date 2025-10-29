# Variables y Outputs - Ejercicio 5

Variables entre pasos de un mismo job:

- Crea un job que:
  - Defina una variable local ``var1`` con valor 1 y la imprima.
  - Defina una variable ``var2`` usando el entorno de GitHub (``$GITHUB_ENV``) con valor 2.
  - Imprima el valor de ``var2`` en un paso diferente.

---
Compartir variables entre pasos usando outputs:

- Crea un job que:
  - Defina un output ``var_step_output`` con valor "valor".
  - Imprima el valor de ``var_step_output`` en un paso posterior.

---
Compartir variables entre jobs:

- Crea un job que:
  - Imprima el valor de ``var2`` y observa qué sucede.
  - Defina un output ``var3`` con valor 3 para compartir con otros jobs.

---
Imprimir variables entre jobs:

- Crea un job que dependa del anterior y:
  - Imprima el valor de ``var3`` definido en el job anterior.

---
Usar variables predefinidas de GitHub:

- Crea un job que imprima las siguientes variables predefinidas por GitHub:
  - Nombre del ``repositorio``.
  - Nombre de la ``rama``.
  - Nombre del ``evento`` que activó el workflow.



```yml
name: Variables - Ejercicio 5  # Nombre del workflow

on:
  push:  # Evento que dispara el workflow al hacer un push
    branches:
      - ejercicios  # Se activa cuando se hace un push a la rama 'ejercicios'

jobs:
  variables_mismo_job:
    runs-on: [self-hosted, labs-runner]  # Define el entorno donde se ejecutará el job
    steps:
    
      - name: Definir variable local
        run: |
          var1=1  # Se define una variable local 'var1'
          echo "el valor de la primera variable es: $var1"  # Se imprime el valor de la variable local

      - name: Definir variable de entorno
        run: |
          echo "var2=2" >> $GITHUB_ENV  # Se define una variable de entorno 'var2' y se guarda en el archivo $GITHUB_ENV

      - name: Imprimir variable de entorno en otro paso
        run: |
          echo "el valor de la segunda variable es: $var2"  # Se intenta imprimir la variable de entorno, pero no funcionará en este paso

  compartir_variables_entre_pasos:
    runs-on: [self-hosted, labs-runner]  # Define el entorno donde se ejecutará el job
    steps:

      - name: Definir output con id
        id: definir_output  # Asignamos un id único al paso para usarlo como referencia
        run: echo "var_step_output=valor" >> $GITHUB_OUTPUT  # Se define una variable 'var_step_output' y se guarda como output en $GITHUB_OUTPUT

      - name: Usar el output en otro paso
        run: echo "El output es ${{ steps.definir_output.outputs.var_step_output }}"  # Se usa el output de un paso anterior

  compartir_variables_entre_jobs:
    runs-on: [self-hosted, labs-runner]  # Define el entorno donde se ejecutará el job
    steps:

      - name: Imprimir variable de entorno en otro job
        run: |
          echo "el valor de la tercera variable es: $var2"  # Intenta imprimir 'var2', pero esta variable no estará disponible entre trabajos

      - name: Definir output de var3
        id: definir_var3  # Asignamos un id único al paso
        run: echo "var3=3" >> $GITHUB_OUTPUT  # Define una nueva variable 'var3' como output

    outputs:
      var3: ${{ steps.definir_var3.outputs.var3 }}  # Se configura un output del job para que sea accesible en otros jobs

  imprimir_variables_entre_jobs:
    needs: compartir_variables_entre_jobs  # Este job depende de que 'compartir_variables_entre_jobs' se ejecute correctamente
    runs-on: [self-hosted, labs-runner]  # Define el entorno donde se ejecutará el job
    steps:

      - name: Imprimir variable de entorno en otro job
        run: |
          echo "el valor de la tercera variable es: ${{ needs.compartir_variables_entre_jobs.outputs.var3 }}"  # Imprime la variable 'var3' extraída del job anterior

  usar_variables_predefinidas_de_GitHub:
    runs-on: [self-hosted, labs-runner]  # Define el entorno donde se ejecutará el job
    steps:

      - name: Imprimir variables predefinidas
        run: |
          echo "Repositorio: ${{ github.repository }}"  # Imprime el nombre del repositorio
          echo "Rama: ${{ github.ref_name }}"  # Imprime el nombre de la rama desde la que se ejecutó el workflow
          echo "Evento que activó el workflow: ${{ github.event_name }}"  # Imprime el tipo de evento que activó el workflow
```

Al ser un ejercicio mucho más extenso he decidido hacer un README aparte de los otros cuatros ejercicios. Puedes encontrarlo [aquí](../../soluciones/variables/README.md). Este ejercicio posee varios trabajos cada uno con una función diferente. El primer trabajo denominado `variables_mismo_job` definimos una variable local y después una variable de entorno que mostramos en otro paso. El segundo job es `compartir_variables_entre_pasos` definimos una variable usando outputs con un id determinado añadiendo esta variable en un paso siguiente a trvés de `${{ steps.definir_output.outputs.var_step_output }}`. El tercer job se basa en alguno anterior ya que usamos la variable definida en el paso anterior (var2) y la mostramos no apareciendo esta ya que una variable de entornos se crean en el job concreto no pudiendo ser usada en otros, y define una nueva variable usando outputs con un id distinto siendo la variable 'var3'. Finalmente el cuarto job se basa en el tercer job y muestra la variable definida en el paso anterior. Por ultimo el quinto job imprime ciertas variables predefinidas de GitHub como el repositorio, la rama y el evento que activó el workflow usando `github.repository`, `github.ref_name` y `github.event_name` respectivamente.


### Ejecución

![Captura sobre el código](../../datos/variables/ejercicio%205/variables%205%20parte%201.png)

![Captura sobre el código](../../datos/variables/ejercicio%205/variables%205%20parte%202.png)

![Captura sobre el código](../../datos/variables/ejercicio%205/variables%205%20parte%203.png)

![Captura sobre el código](../../datos/variables/ejercicio%205/variables%205%20parte%204.png)

![Captura sobre el código](../../datos/variables/ejercicio%205/variables%205%20parte%205.png)