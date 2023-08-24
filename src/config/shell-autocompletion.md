## Autocompletar con Shell

Es posible generar scripts de shell que completen de forma automática los parámetros en `bash`, `elvish`, `fish`, `powershell`, y `zsh`.

### zsh

Primero, el lector debe asegurarse de que, en algun lado de su dispositivo, se encuente el archivo `~/.zshrc` (en caso de no estar, agreguelo):

```sh
autoload -U compinit
compinit -i
```

Despues ejecute lo siguiente:

```sh
forge completions zsh > /usr/local/share/zsh/site-functions/_forge
cast completions zsh > /usr/local/share/zsh/site-functions/_cast
anvil completions zsh > /usr/local/share/zsh/site-functions/_anvil
```

Para sistemas basados en ARM:

```sh
forge completions zsh > /opt/homebrew/completions/zsh/_forge
cast completions zsh > /opt/homebrew/completions/zsh/_cast
anvil completions zsh > /opt/homebrew/completions/zsh/_anvil
```

### fish

```sh
mkdir -p $HOME/.config/fish/completions
forge completions fish > $HOME/.config/fish/completions/forge.fish
cast completions fish > $HOME/.config/fish/completions/cast.fish
anvil completions fish > $HOME/.config/fish/completions/anvil.fish
source $HOME/.config/fish/config.fish
```

### bash

```sh
mkdir -p $HOME/.local/share/bash-completion/completions
forge completions bash > $HOME/.local/share/bash-completion/completions/forge
cast completions bash > $HOME/.local/share/bash-completion/completions/cast
anvil completions bash > $HOME/.local/share/bash-completion/completions/anvil
exec bash
```
