## Integración Continua

### GitHub Actions

Es posible hacer los test de tu proyecto usando Github Actions, acá dejamos un ejemplo del `workflow`:

```yml
on: [push]

name: test

jobs:
  check:
    name: Foundry project
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: recursive

      - name: Install Foundry
        uses: foundry-rs/foundry-toolchain@v1
        with:
          version: nightly

      - name: Run tests
        run: forge test -vvv
```

### Travis CI
También es posible realizar los tests usando Travis CI, acá dejamos un ejemplo del `workflow`:

```yml
language: rust
cache:
  cargo: true
  directories:
    - $HOME/.foundry

install:
  - curl -L https://foundry.paradigm.xyz | bash
  - export PATH=$PATH:$HOME/.foundry/bin
  - foundryup -b master

script:
  - forge test -vvv
```

## GitLab CI

Para realizar test usando GitLab CI, acá dejamos un ejemplo del `workflow`:
Nota: revisar las [política de uso](https://docs.gitlab.com/runner/executors/docker.html#how-pull-policies-work) para obtener una representacón remota.

```yml
variables:
  GIT_SUBMODULE_STRATEGY: recursive

jobs:
  image: ghcr.io/foundry-rs/foundry
  script:
    - forge install
    - forge test -vvv
```
