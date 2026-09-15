# FileBrowser original

## O começo

Antes do FileBrowser Quantum, o TuxFreeTech utilizava o FileBrowser original.

A proposta era simples: ter uma interface web para acessar os arquivos armazenados no servidor sem precisar transformar o servidor inteiro em um serviço de arquivos tradicional.

A instalação foi feita com Docker Compose.

## Como era a instalação

A instalação original utilizava a imagem `filebrowser/filebrowser:v2.23.0`.

O container recebia a porta `8089` do servidor e disponibilizava o serviço na porta `80` dentro do container.

A estrutura básica era esta:

```yaml
services:
  filebrowser:
    image: filebrowser/filebrowser:v2.23.0
    container_name: filebrowser
    ports:
      - "8089:80"
    volumes:
      - /caminho/dos/seus/arquivos:/srv
      - ./filebrowser.db:/database.db
    restart: unless-stopped
```

> Os caminhos acima são exemplos. Os caminhos reais utilizados no TuxFreeTech não são publicados.

A configuração real do projeto utilizava dois volumes principais: um para os arquivos armazenados no servidor e outro para o banco de dados do FileBrowser. A instalação também utilizava `restart: unless-stopped`.

## O que cada parte fazia?

### `image`

```yaml
image: filebrowser/filebrowser:v2.23.0
```

Define qual imagem do FileBrowser seria utilizada.

Fixar uma versão é interessante porque evita que uma atualização automática mude o comportamento do serviço sem que isso seja planejado.

### `ports`

```yaml
ports:
  - "8089:80"
```

Significa:

```text
Servidor:8089
     ↓
Container:80
```

Ou seja, o serviço ficava disponível na porta `8089` do servidor.

A instalação registrada no TuxFreeTech utilizava exatamente esse mapeamento.

### `volumes`

O primeiro volume ligava o armazenamento real do servidor ao diretório `/srv` dentro do FileBrowser.

O segundo mantinha o banco de dados fora do container:

```yaml
- ./filebrowser.db:/database.db
```

Isso é importante porque o container pode ser recriado sem perder o banco de dados.

A configuração original registrada mostra esses dois volumes.

## Funcionou?

Sim.

O FileBrowser original chegou a funcionar normalmente como serviço Docker e ficou disponível na porta configurada.

O container registrado posteriormente ainda mostrava o serviço saudável e o mapeamento `8089 → 80`.

O problema não foi simplesmente "o FileBrowser não funcionava".

O problema apareceu quando começamos a considerar a manutenção futura e o acesso externo.

É aí que começa a próxima parte da história.
