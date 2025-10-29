# Triggers

## Documentación

Los triggers en GitHub Actions son **eventos que activan la ejecución de un workflow.** Estos eventos pueden ser acciones que ocurren en un repositorio, como un push a una rama, la apertura de un pull request, la creación de un tag, o incluso eventos programados como cron jobs.

Los triggers se definen en la sección on de un archivo de workflow, y permiten que los flujos de trabajo se inicien automáticamente cuando ocurren determinadas condiciones, facilitando la automatización de tareas como pruebas, despliegues, y revisiones de código.

[Documentación Triggers](https://docs.github.com/es/actions/writing-workflows/choosing-when-your-workflow-runs/triggering-a-workflow)

[Listado de triggers](https://docs.github.com/es/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows)

> Workflow triggers are events that cause a workflow to run. These events can be:
>
> - Events that occur in your workflow's repository
> - Events that occur outside of GitHub and trigger a repository_dispatch event on GitHub
> - Scheduled times
> - Manual

---

## Ejemplos

```yaml
on: push
```

```yaml
on: [push, fork]
```

```yaml
on:
  issues:
    types:
      - opened
      - labeled
```

```yaml
on:
  push:
    branches:
      - main
      - 'releases/**'
```

---

## Ejercicios

- [Ejercicio 1](ejercicio1.md)
- [Ejercicio 2](ejercicio2.md)
- [Ejercicio 3](ejercicio3.md)
- [Ejercicio 4](ejercicio4.md)
