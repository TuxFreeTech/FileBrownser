# O que é o FileBrowser e por que usamos

## A ideia

O FileBrowser foi escolhido para resolver uma necessidade bastante simples:

**ter acesso aos arquivos do servidor através de uma interface web.**

Em vez de depender de compartilhamentos de rede, programas específicos ou acesso direto ao servidor, a proposta era ter uma página que pudesse ser aberta pelo navegador.

A ideia pode ser resumida assim:

```text
Arquivos no servidor
        ↓
    FileBrowser
        ↓
   navegador web
```

O FileBrowser não foi escolhido para transformar o TuxServer em uma grande plataforma de nuvem.

A proposta era mais simples: disponibilizar os arquivos de forma prática, mantendo o armazenamento no próprio servidor.

---

## Por que isso fazia sentido no TuxFreeTech?

O TuxFreeTech já possuía um servidor capaz de armazenar os arquivos.

Então não fazia muito sentido enviar tudo para um serviço externo apenas para conseguir uma interface de acesso.

O FileBrowser permitia aproveitar a estrutura que já existia:

```text
TuxServer
   ├── armazenamento
   ├── Docker
   └── FileBrowser
          ↓
       acesso web
```

Isso também dava mais controle sobre onde os arquivos estavam armazenados.

---

## O que queríamos conseguir?

Na prática, o objetivo era ter um serviço que permitisse:

- acessar arquivos pelo navegador;
- navegar pelas pastas;
- enviar arquivos;
- baixar arquivos;
- organizar o conteúdo;
- acessar o serviço também fora da rede local;
- manter os arquivos no próprio servidor.

Não era necessário criar uma solução gigantesca.

Era necessário que funcionasse.

---

## Por que usar Docker?

O Docker facilitou bastante a implantação.

Em vez de instalar todos os componentes diretamente no sistema operacional do servidor, o FileBrowser podia funcionar dentro de um container próprio.

A estrutura ficava aproximadamente assim:

```text
TuxServer
   ↓
Docker
   ↓
Container FileBrowser
   ↓
arquivos montados do servidor
```

Isso também facilitava recriar o serviço quando necessário.

A configuração podia ficar registrada em um `docker-compose.yml`, tornando mais fácil entender como o container havia sido criado.

---

## O armazenamento

Um dos pontos importantes era separar o programa dos arquivos.

O FileBrowser precisava enxergar uma pasta do servidor.

No Docker, isso era feito através de um volume:

```yaml
volumes:
  - /caminho/dos/seus/arquivos:/srv
```

A ideia é:

```text
/caminho/dos/seus/arquivos
             ↓
           /srv
             ↓
       FileBrowser
```

O caminho da esquerda pertence ao servidor.

O `/srv` é o caminho que o FileBrowser enxerga dentro do container.

---

## E o acesso externo?

Depois que a utilização local funcionou, surgiu outra necessidade:

**acessar os arquivos de fora da rede.**

Foi aí que entrou o Cloudflare.

O caminho passou a ser aproximadamente:

```text
Internet
   ↓
Cloudflare
   ↓
acesso externo
   ↓
TuxServer
   ↓
FileBrowser
   ↓
arquivos
```

Isso tornou o projeto mais útil, mas também trouxe novos problemas.

Especialmente quando começamos a trabalhar com arquivos grandes.

---

## O ponto em que a história muda

O FileBrowser original funcionava.

Esse detalhe é importante.

A migração para o Quantum não aconteceu porque a primeira solução fosse inútil.

Ela aconteceu principalmente porque o projeto original estava sendo encerrado e não teria continuidade de manutenção e segurança.

A partir daí, começamos a procurar uma alternativa que mantivesse a mesma ideia, mas permitisse continuar evoluindo o serviço.

É aqui que começa a história do **FileBrowser Quantum**.

No próximo capítulo, vamos olhar para a instalação original e para a estrutura que foi usada no TuxFreeTech.
