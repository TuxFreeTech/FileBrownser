# Migração para o FileBrowser Quantum

## Por que migrar?

A decisão de procurar uma alternativa não aconteceu porque o FileBrowser original fosse inútil.

Pelo contrário.

Ele cumpria bem a função para a qual havia sido instalado.

O ponto decisivo foi a continuidade do projeto original.

O próprio FileBrowser informou que estava sendo encerrado e que, após o arquivamento, não haveria novas versões nem correções de segurança.

Para um serviço utilizado através da internet, isso pesou bastante na decisão.

---

## A alternativa

A busca levou ao **FileBrowser Quantum**.

O Quantum mantém a ideia central de um gerenciador de arquivos acessível pela web, mas segue uma linha de desenvolvimento própria.

A instalação pode ser feita com Docker e o projeto utiliza um arquivo de configuração próprio, normalmente chamado `config.yaml`.

---

## A troca

A migração foi tratada como uma mudança de aplicação, e não simplesmente como uma troca de nome de container.

O objetivo era preservar a ideia original:

```text
arquivos no servidor
        ↓
FileBrowser
        ↓
acesso pela web
```

mas construir uma base que pudesse continuar sendo ajustada e mantida.

O container atual do TuxFreeTech é:

```text
filebrowser-quantum
```

e utiliza:

```text
gtstef/filebrowser:beta
```

com a porta:

```text
8089 → 80
```

No registro atual do servidor, o container aparece como saudável.

---

## Uma mudança importante

Durante a migração, a configuração deixou de ser apenas uma pequena definição de Compose.

O Quantum passou a ter uma configuração própria, incluindo opções relacionadas a uploads em partes.

Isso foi fundamental para resolver o problema dos arquivos grandes através do Cloudflare.

A configuração exata utilizada no TuxFreeTech será registrada no próximo capítulo depois de conferirmos os arquivos reais do servidor.

---

## Por que não publicar simplesmente a configuração real?

Porque este repositório é público.

O objetivo é ensinar a estrutura, não publicar a infraestrutura particular do TuxFreeTech.

Por isso, os exemplos deste projeto serão adaptados:

```text
configuração real
       ↓
remoção de dados privados
       ↓
valores de exemplo
       ↓
configuração reproduzível
```

Assim alguém pode entender exatamente o que precisa fazer sem receber informações específicas do nosso servidor.
