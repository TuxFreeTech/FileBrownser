# Configuração final

Este capítulo vai reunir a configuração que ficou funcionando no TuxFreeTech.

A intenção é apresentar duas coisas separadamente:

1. como o serviço está estruturado;
2. como adaptar a estrutura para outro servidor.

## Estrutura

A implantação atual utiliza Docker.

O container registrado atualmente é:

```text
filebrowser-quantum
```

A imagem utilizada é:

```text
gtstef/filebrowser:beta
```

e a porta publicada é:

```text
8089 → 80
```

O container aparece como `healthy` no servidor.

---

## Compose de exemplo

O YAML público será baseado na configuração real, mas com os caminhos e informações privadas substituídos.

Modelo:

```yaml
services:
  filebrowser:
    image: gtstef/filebrowser:beta
    container_name: filebrowser-quantum
    ports:
      - "8089:80"

    volumes:
      - /caminho/dos/seus/arquivos:/srv
      - filebrowser_config:/config
      - filebrowser_db:/database

    restart: unless-stopped

volumes:
  filebrowser_config:
  filebrowser_db:
```

> **Atenção:** este bloco é um modelo de documentação e não deve ser tratado como cópia literal da instalação atual do TuxFreeTech. O Compose definitivo será atualizado após a conferência dos arquivos reais.

---

## Configuração do Quantum

O Quantum utiliza um arquivo `config.yaml` para suas configurações.

Entre as opções relacionadas a uploads existem configurações para:

- quantidade de uploads simultâneos;
- tamanho das partes de upload;
- tamanho das partes de download;
- outras opções do servidor.

O valor utilizado pelo TuxFreeTech será colocado aqui somente depois de recuperarmos o arquivo real.

---

## Por que preservar a configuração em arquivos?

Porque isso torna a instalação reproduzível.

Em vez de depender apenas de configurações feitas manualmente pela interface, podemos manter os arquivos de configuração junto da documentação.

Assim, no futuro, fica muito mais fácil:

```text
perdeu a configuração?
        ↓
abre o YAML
        ↓
entende a estrutura
        ↓
recria o serviço
```

---

## O que será substituído nos exemplos públicos?

Valores privados serão transformados em exemplos.

Por exemplo:

```yaml
- /dados/reais/do-servidor:/srv
```

vira:

```yaml
- /caminho/dos/seus/arquivos:/srv
```

O mesmo vale para qualquer endereço, identificador ou informação que pertença exclusivamente à infraestrutura do TuxFreeTech.

---

## Configuração final real

**Pendente de conferência no TuxServer.**

Este marcador é intencional.

Não vamos completar esta parte com uma configuração presumida.

Depois de recuperar o `docker-compose.yml` e o `config.yaml` atuais, este capítulo será atualizado com a estrutura real e sua versão pública correspondente.
