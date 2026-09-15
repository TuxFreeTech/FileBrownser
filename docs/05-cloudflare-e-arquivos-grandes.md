# Cloudflare e arquivos grandes

## O problema

Um dos pontos mais importantes da implantação apareceu quando o FileBrowser passou a ser utilizado através do acesso externo.

O Cloudflare possui limite para o tamanho do corpo de uma requisição.

Atualmente, nos planos Free e Pro, esse limite é de 100 MB por requisição.

Quando uma requisição ultrapassa o limite, o Cloudflare pode retornar:

```text
413 Payload Too Large
```

---

## Por que isso é importante?

Imagine um arquivo de 4 GB.

Se o aplicativo tentar enviar:

```text
4 GB
 ↓
1 requisição
 ↓
Cloudflare
```

a requisição inteira será maior que o limite permitido.

O problema não está necessariamente no arquivo.

Está na forma como ele está sendo enviado.

---

## A solução: upload em partes

O FileBrowser Quantum possui configuração para controlar o tamanho das partes usadas nos uploads.

A ideia é transformar:

```text
4 GB
 ↓
uma requisição enorme
```

em algo como:

```text
4 GB
 ↓
partes menores
 ↓
várias requisições
 ↓
Cloudflare
 ↓
Quantum
 ↓
arquivo completo
```

Cada requisição fica abaixo do limite individual.

---

## O limite não desaparece

É importante deixar isso muito claro.

O Cloudflare continua tendo um limite por requisição.

O que conseguimos fazer é evitar que um arquivo grande seja enviado em uma única requisição.

Por isso, neste projeto, a expressão mais correta é:

> **sem restrição prática de tamanho para arquivos grandes, dentro das condições do sistema e do armazenamento disponível.**

Não significa que exista um tamanho infinito ou que o Cloudflare tenha deixado de possuir limites.

---

## Por que não colocar o valor real aqui ainda?

Porque esta documentação deve registrar a configuração que realmente está rodando.

O valor exato do `uploadChunkSizeMb` utilizado no TuxFreeTech será inserido depois da conferência do `config.yaml` atual.

Não vamos inventar um número apenas para preencher a documentação.

Essa regra vale para todo este repositório.

---

## O que também precisa ser considerado

O tamanho do arquivo não é o único fator.

Uploads grandes também dependem de:

- espaço disponível no servidor;
- estabilidade da conexão;
- navegador;
- configuração do Quantum;
- configuração do Cloudflare;
- tempo necessário para concluir o envio;
- comportamento do cliente durante uploads em várias partes.

Por isso, depois de configurar o sistema, é importante testar arquivos pequenos, médios e grandes.

---

## Resultado

A utilização de uploads em partes tornou possível contornar, na prática, o limite de uma única requisição do Cloudflare.

Esse foi um dos ajustes mais importantes da configuração atual.

O capítulo seguinte documentará a configuração final completa.
