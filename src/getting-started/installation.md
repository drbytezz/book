## Instalación

Si encuentras algún problema durante la instalación, consulta la [FAQ](../faq.md).

### Binarios precompilados

Los binarios están disponibles directamente desde la [página de las versiones en GitHub ](https://github.com/foundry-rs/foundry/releases).
Dischos binarios son mejor gestionadas utilizando [Foundryup](#using-foundryup).

### Utilizando Foundryup
Foundryup es el instalador del set de herramientas de Foundry. Podés encontrar mas informacion sobre el tema [aquí](https://github.com/foundry-rs/foundry/blob/master/foundryup/README.md).

Abrí tu terminal y ejecutar el siguiente comando:

```sh
curl -L https://foundry.paradigm.xyz | bash
```
Esto va a instalar Foundryup, después, simplemente seguí las instrucciones que aparecen en pantalla, 
esto va a volver disponible el comando `foundryup` en tu CLI.

Al ejecutar el comando `foundryup`, se va a instalar la ultima versión (nocturna?) [binarios precompilados](#precompiled-binaries): `forge`, `cast`, `anvil`, y `chisel`.
Para obtener más opciones, utilizá `foundryup --help`, como puede ser el caso de que se quiera instalar una version especifica del comando un un `commit` particular.

> ℹ️ **Nota**
>
> Si estas instalando desde Windows, vas a necesitar ysar (e instalar) [Git BASH](https://gitforwindows.org/) o [WSL](https://learn.microsoft.com/en-us/windows/wsl/install),
> como tu terminal, esto se debe a que al momento de la fecha, Foundryup no tiene soporte para Powershell o Cmd.

### Instalando desde la fuente

#### Prerequisitos
Vas a necesitar el compilador de  [Rust](https://rust-lang.org) y Cargo, siendo este el controlador de paquetes de Rust.
La manera más sencilla de instalar ambos es usando  [`rustup.rs`](https://rustup.rs/).

Para Windows, vas a necesitar la versión más reciente de [Visual Studio](https://visualstudio.microsoft.com/downloads/), instalada con el "Desktop Development With C++" como extensión descargada.

#### Construcción

Podés optar por usar una versión diferente de [Foundryup](#using-foundryup):

```sh
foundryup --branch master
foundryup --path path/to/foundry
```
O, usando un simple comando de Cargo:

```sh
cargo install --git https://github.com/foundry-rs/foundry --profile local forge cast chisel anvil
```
O, manualmente construyendolo desde una copia local del [Repositorio de Foundry](https://github.com/foundry-rs/foundry):

```sh
# Clona el Repositorio
git clone https://github.com/foundry-rs/foundry.git
cd foundry
# Instala Forge
cargo install --path ./crates/forge --profile local --force
# Instala Cast
cargo install --path ./crates/cast --profile local --force
# Instala Anvil
cargo install --path ./crates/anvil --profile local --force
# Instala Chisel
cargo install --path ./crates/chisel --profile local --force
```

### Instalando para CI en  Github Action

Véase [foundry-rs/foundry-toolchain](https://github.com/foundry-rs/foundry-toolchain) GitHub Action.

### Usando Foundry con Docker

Foundry puede ser utilizado dentro de un contenedor de Docker. Si no lo tenés, Docker se puede instalar directo de la [web de Docker](https://docs.docker.com/get-docker/).

Una vez instalado, podés instalar la ultima versión utilizando el siguiente comando:

```sh
docker pull ghcr.io/foundry-rs/foundry:latest
```
También es posible construir una representación local de docker. Desde la carpeta de Foundry, corré lo siguiente:
```sh
docker build -t foundry .
```

Para más ejemplos y guias de como usar esta representación, revisa la [sección de tutoriales de Docker](../tutorials/foundry-docker).

> ℹ️ **Nota**
>
> Algunos equipos(incluidos aquellos que llevan el chip M1), puede que no sean capaces de montar la representación local de docker. Este es un problema frecuente.
