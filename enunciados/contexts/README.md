# Contexts

## Documentación

Los contexts en GitHub Actions son conjuntos de información predefinida que se puede utilizar dentro de los flujos de trabajo (workflows). Estos contexts proporcionan datos relevantes sobre el entorno de ejecución, los eventos que desencadenaron el flujo de trabajo, las propiedades del repositorio, y más.

Principales contexts en GitHub Actions:

- **github:** Proporciona información sobre el repositorio y el evento que activó el workflow. Incluye datos como el nombre del repositorio, el actor que disparó el evento, y el SHA de la confirmación (commit) actual.

- **env:** Contiene las variables de entorno definidas en el archivo de workflow o en la configuración de GitHub.

- **job:** Proporciona información sobre el trabajo (job) actual, como su estado (status) y su ID.

- **steps:** Permite acceder a la salida (output) de los pasos (steps) previos dentro del mismo trabajo.

- **secrets:** Accede a los secretos almacenados en GitHub, como tokens de acceso o claves API, de forma segura.

- **runner:** Proporciona detalles sobre el entorno en el que se está ejecutando el workflow, incluyendo el sistema operativo, el tipo de máquina, y más.

- **matrix** Se utiliza en workflows que utilizan matrices de configuración para realizar pruebas en diferentes entornos. Este context contiene las combinaciones específicas de la matriz que se están ejecutando en un momento dado.

[Documentación sobre Contextos](https://docs.github.com/es/actions/writing-workflows/choosing-what-your-workflow-does/accessing-contextual-information-about-workflow-runs)

---

## Ejemplos

### Ejemplo de uso del contexto "github"

```yaml
name: Run CI
on: [push, pull_request]

jobs:
  normal_ci:
    runs-on: ubuntu-latest
    steps:
      - name: Run normal CI
        run: echo "Running normal CI"

  pull_request_ci:
    runs-on: ubuntu-latest
#             ↓
    if: ${{ github.event_name == 'pull_request' }} 
    steps:
      - name: Run PR CI
        run: echo "Running PR only CI"
```

### Ejemplo de uso del contexto "env"

```yaml
name: Hi Mascot
on: push
env:
  mascot: Mona
  super_duper_var: totally_awesome

jobs:
  windows_job:
    runs-on: windows-latest
    steps:
      - run: echo 'Hi ${{ env.mascot }}'  # Hi Mona
      - run: echo 'Hi ${{ env.mascot }}'  # Hi Octocat
#        ↓
        env:
          mascot: Octocat
  linux_job:
    runs-on: ubuntu-latest
    env:
      mascot: Tux
    steps:
      - run: echo 'Hi ${{ env.mascot }}'  # Hi Tux
```

---

## Ejercicios

- [Ejercicio 1](ejercicio1.md)
- [Ejercicio 2](ejercicio2.md)
- [Ejercicio 3](ejercicio3.md)
- [Ejercicio 4](ejercicio4.md)
