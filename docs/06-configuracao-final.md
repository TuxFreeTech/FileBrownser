# Configuração final

Este capítulo reúne a configuração que ficou funcionando no TuxFreeTech.

A intenção é apresentar:

1. como o serviço está estruturado;
2. quais arquivos fazem parte da configuração;
3. como a instalação pode ser adaptada para outro servidor;
4. quais cuidados devem ser tomados para não perder as alterações personalizadas.

---

## Estrutura

A implantação atual utiliza Docker.

O container registrado atualmente é:

filebrowser-quantum

A imagem utilizada é:

gtstef/filebrowser:beta

A versão identificada na imagem utilizada é:

v2.0.4-beta

A porta publicada pelo Docker é:

8089 → 80

No servidor, o container funciona com estado `healthy`.

---

## Compose utilizado no TuxFreeTech

A estrutura atual do Compose é:

services:
  filebrowser:
    container_name: filebrowser-quantum
    image: gtstef/filebrowser:beta
    ports:
      - "8089:80"
    volumes:
      - ./data:/home/filebrowser/data
      - ./branding:/branding:ro
      - /caminho/dos/arquivos:/srv
    restart: unless-stopped

Os caminhos `./data` e `./branding` são relativos ao diretório onde está localizado o arquivo `compose.yaml`.

O caminho `/caminho/dos/arquivos` representa, na documentação pública, o local onde os arquivos do servidor serão armazenados.

Na instalação real do TuxFreeTech, esse caminho aponta para o armazenamento utilizado pelo servidor.

O diretório `branding` é montado como somente leitura:

./branding → /branding

Isso mantém os arquivos de identidade visual separados dos arquivos internos do aplicativo.

---

## Organização dos arquivos

A instalação utiliza a seguinte estrutura:

filebrowser-quantum/
├── compose.yaml
├── data/
│   ├── config.yaml
│   ├── config.yaml.bak
│   ├── filebrowser.sqlite
│   └── tmp/
└── branding/
    ├── tuxcloud-mascote.png
    ├── tuxcloud-login.css
    └── TuxFreeTech-favicon-256.png

Os nomes e a organização dos arquivos personalizados são mantidos separados da estrutura interna do container.

Isso facilita backups, manutenção e futuras atualizações da imagem.

---

## Configuração do Quantum

O Quantum utiliza um arquivo `config.yaml` para suas configurações principais.

A configuração atual contém:

http:
  port: 80
  listen: "0.0.0.0"

frontend:
  name: "TuxCloud"
  favicon: "/branding/TuxFreeTech-favicon-256.png"
  styling:
    customCSS: "/branding/tuxcloud-login.css"
  loginIcon: "/branding/tuxcloud-mascote.png"

server:
  database:
    path: "/home/filebrowser/data/filebrowser.sqlite"
  cacheDir: "/home/filebrowser/data/tmp"
  sources:
    - path: "/srv"
      config:
        defaultEnabled: true

auth:
  adminUsername: "admin"
  adminPassword: "<SENHA_DO_ADMIN>"

A senha real do administrador não faz parte da documentação pública.

O valor `<SENHA_DO_ADMIN>` é apenas um marcador para indicar onde uma senha própria deve ser configurada.

---

## Identidade visual

A configuração do Quantum também foi utilizada para transformar a instalação em um produto com identidade própria.

O nome apresentado na interface é:

TuxCloud

O ícone utilizado na tela de login é o mascote do TuxFreeTech:

/branding/tuxcloud-mascote.png

O favicon utilizado pelo serviço é:

/branding/TuxFreeTech-favicon-256.png

O CSS personalizado é carregado através de:

/branding/tuxcloud-login.css

Os arquivos de identidade visual ficam fora da imagem Docker e são montados no container através do diretório:

./branding:/branding:ro

Essa separação é importante porque permite atualizar ou ajustar a identidade visual sem alterar diretamente os arquivos internos do container.

---

## CSS personalizado

O arquivo utilizado para a personalização visual do login contém as regras responsáveis pelo ajuste do mascote, da área de login e da aparência geral da interface.

O CSS atualmente documentado é:

/* TuxCloud login visual */
body:has(img[src*="tuxcloud-mascote"]) {
  background: #0d0d0f;
  position: relative;
}

body:has(img[src*="tuxcloud-mascote"])::before {
  content: "";
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.48);
  backdrop-filter: blur(2px);
  -webkit-backdrop-filter: blur(2px);
  pointer-events: none;
  z-index: 0;
}

body:has(img[src*="tuxcloud-mascote"]) #app {
  position: relative;
  z-index: 1;
}

body:has(img[src*="tuxcloud-mascote"]) img[src*="tuxcloud-mascote"] {
  width: 180px !important;
  height: auto !important;
  max-height: 190px !important;
  object-fit: contain;
  display: block;
  margin: 0 auto 6px auto;
  filter: drop-shadow(0 10px 24px rgba(0,0,0,.35));
}

body:has(img[src*="tuxcloud-mascote"]) img[src*="tuxcloud-mascote"] + * {
  margin-top: 0;
}

/* Keep the login surface readable without making it heavy. */
body:has(img[src*="tuxcloud-mascote"]) #app form {
  background: rgba(18, 18, 20, 0.62);
  border: 1px solid rgba(255, 255, 255, 0.12);
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.35);
  backdrop-filter: blur(14px);
  -webkit-backdrop-filter: blur(14px);
  border-radius: 18px;
  padding: 24px;
}

@media (max-width: 700px) {
  body:has(img[src*="tuxcloud-mascote"]) img[src*="tuxcloud-mascote"] {
    width: 135px !important;
    max-height: 145px !important;
  }
}

A tentativa anterior de utilizar uma imagem de wallpaper diretamente como fundo não apresentou o resultado visual esperado na interface.

Por isso, a configuração documentada não depende dessa imagem para o funcionamento da identidade visual atual.

O CSS é mantido como arquivo externo justamente para que a personalização não dependa de alterações feitas diretamente dentro da imagem Docker.

A aparência atualmente aprovada mantém o mascote centralizado e o nome `TuxCloud` abaixo dele, criando uma identidade própria para o serviço.

---

## Banco de dados e cache

O banco utilizado pelo Quantum fica em:

/home/filebrowser/data/filebrowser.sqlite

No host, ele corresponde ao arquivo:

./data/filebrowser.sqlite

O diretório de cache utilizado pelo serviço é:

/home/filebrowser/data/tmp

No host:

./data/tmp

O arquivo de configuração possui também uma cópia de segurança:

./data/config.yaml.bak

Manter essa cópia é uma proteção simples contra erros durante alterações futuras.

---

## Armazenamento dos arquivos

O armazenamento principal é disponibilizado ao container através de:

/caminho/dos/arquivos:/srv

Dentro do Quantum, `/srv` é a fonte de arquivos utilizada pelo serviço.

Na configuração atual:

sources:
  - path: "/srv"
    config:
      defaultEnabled: true

O caminho real utilizado no TuxFreeTech não é publicado nesta documentação.

---

## Uploads e Cloudflare

Um dos problemas enfrentados durante a implantação foi a limitação de tamanho de upload imposta pelo Cloudflare.

O limite de uma requisição individual do proxy pode impedir o envio direto de arquivos grandes.

A solução utilizada no projeto foi aproveitar o mecanismo de upload em partes disponibilizado pelo serviço, permitindo que arquivos grandes sejam enviados em múltiplos blocos menores em vez de depender de uma única requisição contendo todo o arquivo.

Essa abordagem foi importante para permitir o uso do TuxCloud através do acesso externo protegido pelo Cloudflare.

O tamanho histórico exato utilizado para cada parte não será registrado como um valor específico porque esse valor não está presente no `config.yaml` atualmente disponível para documentação.

Portanto, esta documentação registra o princípio da solução, sem inventar um número que não possa ser confirmado.

O funcionamento pode ser entendido desta forma:

arquivo grande
      ↓
divisão em partes
      ↓
upload das partes
      ↓
reconstrução do arquivo

Isso reduz a dependência de uma única requisição maior que o limite aceito pelo proxy.

---

## Por que preservar a configuração em arquivos?

Porque isso torna a instalação reproduzível.

Em vez de depender apenas de configurações feitas manualmente pela interface, os principais arquivos ficam organizados junto da documentação.

Assim, no futuro, fica muito mais fácil:

perdeu a configuração?
        ↓
recupera os arquivos
        ↓
entende a estrutura
        ↓
recria o serviço
        ↓
valida o funcionamento

A documentação funciona como uma espécie de memória externa da instalação.

---

## O que será substituído nos exemplos públicos?

Valores privados serão transformados em exemplos.

Por exemplo, um caminho real:

- /dados/reais/do-servidor:/srv

é apresentado publicamente como:

- /caminho/dos/arquivos:/srv

O mesmo princípio vale para:

- senhas;
- caminhos internos específicos;
- identificadores privados;
- informações exclusivas da infraestrutura;
- qualquer outro dado que não seja necessário para reproduzir a estrutura.

A ideia é manter a documentação útil sem transformar o repositório público em um mapa da infraestrutura privada.

---

## O que não deve ser feito

Não é recomendado editar arquivos diretamente dentro do container.

Alterações feitas dessa maneira podem desaparecer quando o container for recriado ou atualizado.

Também não é recomendado misturar os arquivos personalizados diretamente com os arquivos internos da aplicação.

A estrutura utilizada neste projeto evita isso mantendo:

configuração
    ↓
data/

identidade visual
    ↓
branding/

aplicação
    ↓
container Docker

Essa separação reduz a quantidade de coisas que precisam ser refeitas durante uma atualização.

---

## Alterações futuras

Ao alterar a configuração, a preferência é sempre fazer uma mudança por vez.

O fluxo recomendado é:

alterar
   ↓
salvar
   ↓
validar
   ↓
reiniciar o serviço se necessário
   ↓
testar
   ↓
documentar

Antes de uma alteração importante no `config.yaml`, é recomendável manter uma cópia funcional do arquivo.

No projeto atual existe:

config.yaml.bak

Essa abordagem simples evita transformar uma pequena alteração em uma caça ao fantasma dentro do YAML.

---

## Princípio da configuração

A configuração do TuxCloud segue a mesma filosofia utilizada no restante do projeto:

> Alterar o mínimo necessário.

Se uma solução mais simples consegue o mesmo resultado, ficamos com a solução mais simples.

A ideia não é transformar o FileBrowser Quantum em um projeto diferente.

A ideia é utilizar o que a ferramenta oferece, acrescentando apenas o necessário para atender às necessidades do TuxFreeTech.

---

## Estado atual

A instalação atual está funcionando com:

Container:
filebrowser-quantum

Imagem:
gtstef/filebrowser:beta

Versão identificada:
v2.0.4-beta

Porta:
8089 → 80

Estado:
healthy

Nome exibido:
TuxCloud

Login personalizado:
sim

Mascote personalizado:
sim

Favicon personalizado:
sim

CSS externo:
sim

Acesso externo:
Cloudflare Tunnel

A identidade visual atual foi mantida separada da aplicação através do diretório `branding`.

A configuração do serviço e os dados persistentes ficam no diretório `data`.

---

## Resultado

O resultado final não é apenas uma instalação padrão do FileBrowser Quantum.

A estrutura atual separa:

aplicação
+
configuração
+
dados
+
identidade visual
+
acesso externo

Isso permite que o serviço continue sendo administrável como uma aplicação Docker, mas com uma identidade própria dentro do ecossistema TuxFreeTech.

A documentação pública mantém essa mesma lógica: mostrar o suficiente para que outra pessoa consiga entender e reproduzir a estrutura, sem expor informações privadas da infraestrutura original.