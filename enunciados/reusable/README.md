# Workflows reusables

## Documentación

Los workflows reusables en GitHub Actions son flujos de trabajo que pueden ser definidos una vez y luego invocados desde otros repositorios o workflows. Esto permite centralizar la lógica común y reducir la duplicación de código en múltiples proyectos.

Un workflow reusable se define en un repositorio, y otros workflows pueden llamarlo utilizando la palabra clave uses. Al invocar un workflow reusable, se pueden pasar parámetros personalizados para adaptar su comportamiento a diferentes contextos. Esto es útil para estandarizar procesos de CI/CD y mantener la consistencia en la automatización entre múltiples proyectos.

[Documentación Workflows Reusables](https://docs.github.com/es/actions/sharing-automations/reusing-workflows)

---

## Ejemplos

### Workflow reusable

```yaml
name: Reusable workflow example

on:
  workflow_call:
    inputs:
      config-path:
        required: true
        type: string
    secrets:
      token:
        required: true

jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/labeler@v4
      with:
        repo-token: ${{ secrets.token }}
        configuration-path: ${{ inputs.config-path }}
```

### Llamadas a diferentes workflows reusables

```yaml
jobs:
  call-workflow-1-in-local-repo:
    uses: octo-org/this-repo/.github/workflows/workflow-1.yml@172239021f7ba04fe7327647b213799853a9eb89
  call-workflow-2-in-local-repo:
    uses: ./.github/workflows/workflow-2.yml
  call-workflow-in-another-repo:
    uses: octo-org/another-repo/.github/workflows/workflow.yml@v1
```

---

## Ejercicios

- [Ejercicio 1](ejercicio1.md)
- [Ejercicio 2](ejercicio2.md)
- [Ejercicio 3](ejercicio3.md)
