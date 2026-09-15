# FileBrowser Quantum no TuxFreeTech

Documentação da implantação, migração, ajustes e soluções utilizadas para transformar o FileBrowser Quantum em uma ferramenta de acesso aos arquivos do TuxFreeTech pela web.

> **Objetivo desta documentação:** registrar o que foi feito de verdade, inclusive erros, tentativas, problemas e soluções. A ideia é servir como histórico do projeto e também como referência para quem quiser montar algo parecido.

---

## O que é este projeto?

O FileBrowser Quantum é um gerenciador de arquivos que permite acessar e administrar arquivos através de uma interface web.

Neste projeto, ele foi utilizado como uma espécie de "nuvem particular" para acessar arquivos armazenados no servidor do TuxFreeTech, inclusive de fora da rede local.

O acesso público utilizado no projeto é:

```text
https://files.tuxfree.me/
```

Os arquivos continuam armazenados no servidor. O navegador fornece a interface para acessá-los, enviar novos arquivos, baixar, organizar e realizar outras operações permitidas pelo sistema.

---

## Por que este projeto existe?

A história começou com o **FileBrowser original**.

Ele funcionava bem para a finalidade proposta, mas o projeto original entrou em processo de encerramento e foi posteriormente arquivado. A última versão planejada foi publicada antes do arquivamento e o projeto deixou de receber novas versões, correções de bugs e correções de segurança.

Isso criou um problema importante para um serviço que fica exposto à internet:

> Mesmo que o programa continue funcionando, não existe mais a mesma perspectiva de manutenção futura.

Foi então que começou a busca por uma alternativa que mantivesse a ideia simples do FileBrowser, mas continuasse sendo desenvolvida.

Foi nesse momento que entrou o **FileBrowser Quantum**.

O Quantum é um fork do projeto original, com uma interface renovada, configuração por `config.yaml` e vários recursos adicionais. O projeto disponibiliza imagens Docker próprias para implantação.

---

## O que esta documentação vai mostrar?

Este repositório não pretende ser apenas um tutorial de instalação.

Ele registra a evolução do projeto.

A documentação vai mostrar:

- como o FileBrowser original foi instalado;
- como o armazenamento foi ligado ao container;
- como o acesso externo foi configurado;
- os problemas encontrados com o Cloudflare;
- erros que apareceram durante os testes;
- tentativas que não funcionaram;
- soluções que foram adotadas;
- por que a migração para o Quantum aconteceu;
- como o Quantum foi instalado;
- como a configuração foi sendo ajustada;
- como os uploads de arquivos grandes foram tratados;
- exemplos dos arquivos YAML utilizados;
- cuidados para evitar os mesmos problemas;
- como chegar a uma instalação mais previsível e fácil de manter.

A intenção é **não esconder a parte feia do processo**.

Se alguma coisa deu errado, ela faz parte da história.

---

## Cloudflare e arquivos grandes

Um dos pontos mais importantes deste projeto apareceu quando o acesso externo passou pelo Cloudflare.

O Cloudflare possui um limite para o tamanho do corpo de uma requisição. Nos planos Free e Pro, por exemplo, o limite é de 100 MB por requisição. Quando esse limite é ultrapassado, o Cloudflare pode responder com `413 Payload Too Large`.

Isso é particularmente importante para um gerenciador de arquivos.

Um arquivo de vários gigabytes não pode simplesmente ser enviado como uma única requisição através de uma camada que aceite apenas 100 MB.

A solução adotada no projeto foi trabalhar com **upload em partes (chunks)**.

Em vez de:

```text
arquivo de 4 GB
       ↓
uma única requisição
       ↓
Cloudflare
       ↓
bloqueio
```

o arquivo pode ser dividido em várias partes menores:

```text
arquivo de 4 GB
       ↓
┌──────┬──────┬──────┬──────┬──────┐
│ parte│ parte│ parte│ parte│ ...  │
└──────┴──────┴──────┴──────┴──────┘
       ↓
várias requisições menores
       ↓
Cloudflare
       ↓
FileBrowser Quantum
       ↓
arquivo reconstruído no servidor
```

Isso não remove o limite do Cloudflare.

O que muda é a forma como o arquivo é transportado.

Na prática, isso permite trabalhar com arquivos muito maiores do que o limite de uma única requisição, desde que o aplicativo faça o envio em partes menores. A própria documentação do Cloudflare recomenda dividir requisições grandes em partes menores quando necessário.

O valor exato utilizado no TuxFreeTech será documentado posteriormente, junto com a configuração real do Quantum.

---

## Estrutura da documentação

A documentação será organizada por etapas:

```text
docs/
├── 01-o-que-e-e-por-que-usamos.md
├── 02-filebrowser-original.md
├── 03-problemas-e-erros.md
├── 04-migracao-para-quantum.md
├── 05-cloudflare-e-arquivos-grandes.md
├── 06-configuracao-final.md
└── 07-manutencao-e-o-que-evitar.md
```

Também serão mantidos exemplos de configuração:

```text
config/
├── filebrowser-original.yml
├── filebrowser-quantum.yml
└── README.md
```

E imagens utilizadas para explicar as etapas:

```text
images/
├── filebrowser-original.png
├── filebrowser-quantum.png
└── ...
```

---

## Sobre os arquivos YAML

Os arquivos YAML são uma parte importante desta documentação.

Eles ajudam a mostrar exatamente como uma instalação desse tipo pode ser estruturada.

Porém, **não serão publicados dados privados do TuxFreeTech**.

Informações como:

- caminhos reais do servidor;
- nomes internos;
- endereços privados;
- senhas;
- tokens;
- identificadores;
- configurações específicas da infraestrutura;

serão substituídas por exemplos.

Por exemplo, em vez de publicar:

```yaml
volumes:
  - /caminho-real-do-servidor:/srv
```

a documentação poderá mostrar:

```yaml
volumes:
  - /caminho/dos/seus/arquivos:/srv
```

A estrutura continua sendo real e reproduzível.

Quem estiver montando o próprio servidor só precisa substituir os valores de exemplo pelos seus.

---

## O princípio desta documentação

Este projeto segue uma regra simples:

> **Não documentar para impressionar. Documentar para permitir que outra pessoa entenda, reproduza e tenha vontade de tentar.**

Por isso, a documentação procura evitar:

- linguagem excessivamente técnica;
- comandos desnecessários;
- configurações inventadas;
- passos escondidos;
- soluções complicadas para problemas simples.

Quando um termo técnico for realmente necessário, ele será explicado.

---

## Importante: esta não é uma instalação "genérica"

Embora os exemplos sejam preparados para outras pessoas conseguirem reproduzir o projeto, esta documentação nasceu de uma instalação real.

Isso significa que algumas decisões foram tomadas por causa da infraestrutura existente no TuxFreeTech.

A documentação vai separar claramente:

**O que aconteceu no TuxFreeTech**

de

**O que pode ser adaptado para outro servidor.**

Assim evitamos transformar uma configuração específica em uma falsa "receita universal".

---

## Estado atual

No momento desta documentação, o FileBrowser Quantum está funcionando no TuxServer através de Docker.

A implantação atual utiliza:

```text
Container: filebrowser-quantum
Imagem:    gtstef/filebrowser:beta
Porta:     8089 → 80
Status:    healthy
```

A configuração exata utilizada no TuxFreeTech será registrada nos próximos documentos depois de conferirmos os arquivos reais do servidor.

---

## O que não será publicado

Este repositório é público.

Por isso, nunca serão publicados:

- senhas;
- tokens;
- chaves;
- cookies;
- credenciais;
- arquivos pessoais;
- caminhos que revelem informações desnecessárias da infraestrutura;
- configurações privadas do Cloudflare;
- qualquer outro segredo utilizado pelo servidor.

Os exemplos serão suficientes para reproduzir a estrutura sem transformar a documentação em uma porta aberta para o servidor de outra pessoa.

---

## Para quem esta documentação pode ser útil?

Principalmente para quem quer montar algo simples como:

```text
Servidor próprio
      ↓
Docker
      ↓
FileBrowser Quantum
      ↓
Cloudflare / acesso externo
      ↓
Navegador
      ↓
Seus arquivos
```

Também pode ser útil para quem já utiliza o FileBrowser original e precisa avaliar uma migração.

---

## Licença e projeto original

Este repositório documenta a implantação realizada pela TuxFreeTech.

O FileBrowser e o FileBrowser Quantum são projetos independentes, mantidos por seus respectivos autores e distribuídos sob suas próprias licenças.

Consulte os repositórios oficiais antes de utilizar ou redistribuir qualquer componente.

---

## Próximos capítulos

A documentação será construída na ordem em que o projeto aconteceu:

1. **O que é o FileBrowser e por que ele foi escolhido**
2. **A primeira instalação do FileBrowser original**
3. **Os problemas encontrados**
4. **A decisão de migrar para o Quantum**
5. **Cloudflare e uploads grandes**
6. **A configuração final**
7. **Manutenção, cuidados e o que evitar**

A ideia é que, ao terminar, alguém consiga começar pelo início e entender não apenas **como fazer**, mas também **por que cada decisão foi tomada**.
