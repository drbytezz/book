## Configurando con `foundry.toml`

Forge se puede configurar utilizando un archivo de configuración llamado `foundry.toml`, que se coloca en la raíz de tu proyecto.

Podemos organizar la configuración por perfiles. El perfil predeterminado se llama `default`, del cual heredan todos los demás perfiles. Eres libre de personalizar el perfil `default` y agregar tantos perfiles nuevos como sean necesarios.

Además, el lector puede crear un archivo global `foundry.toml` que este en la carpeta base.

Echemos un vistazo a un archivo de configuración que contiene dos perfiles: el perfil predeterminado, que siempre habilita el optimizador, y un perfil de CI, que siempre muestra trazas:

```toml
[profile.default]
optimizer = true
optimizer_runs = 20_000

[profile.ci]
verbosity = 4
```

Cuando ejecutamos el comando `forge`, el lector puede especificar con que perfil este desea ejecutarlo, esto se logra usando la variable de ambiente `FOUNDRY_PROFILE` .

### Secciones independientes

Además de las secciones de perfil, el archivo de configuración también puede contener secciones independientes ([fmt], [fuzz], [invariant], etc.). Por defecto, cada sección independiente pertenece al perfil default.
es decir, [fmt] es equivalente a [profile.default.fmt].

Para configurar alguna de las secciones, ya sea en terminos generales(`default`) o para perfiles especificos, usa el siguiente sintaxis `[profile.<profile name>.<standalone>]`.
i.e. `[profile.ci.fuzz]`.

<br>

> 📚 **Referencia**
> 
> Consulta la  [Referencia de `foundry.toml`](../reference/config/) para tener un pantallazo general de los parametros que se pueden configurar.
