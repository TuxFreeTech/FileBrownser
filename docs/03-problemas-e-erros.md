# Problemas e erros encontrados

Esta parte existe justamente para registrar o que não funcionou perfeitamente.

Uma documentação útil não deve mostrar somente o resultado final. Os erros ajudam a entender por que determinadas decisões foram tomadas.

## 1. O projeto original estava chegando ao fim

Durante a utilização do FileBrowser original, apareceu um aviso importante nos próprios logs do container:

```text
NOTICE: File Browser is being wound down.
NOTICE: The project is archived on 2026-09-01, after which there will be no
NOTICE: further releases and no security fixes.
```

Esse aviso foi registrado diretamente no servidor.

O problema aqui não era uma falha imediata.

O serviço continuava funcionando.

O problema era outro:

> Um serviço exposto à internet não deveria depender indefinidamente de um projeto que não receberia mais correções de segurança.

Esse foi um dos principais motivos para procurar uma alternativa.

---

## 2. Problemas de acesso e autenticação durante os testes

Nos testes registrados nos logs também apareceram respostas como:

```text
401
403
```

inclusive em tentativas de acesso e login:

```text
/teste/: 401
/api/login: 403
```



Esses registros fazem parte da fase de testes e troubleshooting.

É importante não interpretar automaticamente cada `401` ou `403` como uma falha permanente do sistema. Respostas desse tipo também podem acontecer quando uma tentativa de acesso não está autenticada ou quando uma requisição não atende ao comportamento esperado pela aplicação.

Por isso, a documentação registra o acontecimento sem transformar o log em uma conclusão que ele não prova.

---

## 3. O acesso externo trouxe outra camada de problemas

Quando um serviço funciona somente dentro da rede local, o caminho é relativamente curto:

```text
Navegador
   ↓
Servidor
   ↓
FileBrowser
```

Com acesso externo, o caminho passa a envolver outras peças:

```text
Navegador
   ↓
Internet
   ↓
Cloudflare
   ↓
Túnel / proxy
   ↓
Servidor
   ↓
FileBrowser
```

Cada camada pode impor suas próprias regras.

Isso se tornou especialmente importante para uploads.

---

## 4. O problema dos arquivos grandes

O Cloudflare limita o tamanho do corpo de uma requisição.

Nos planos Free e Pro, o limite informado atualmente é de 100 MB por requisição. Quando uma requisição ultrapassa o limite, pode ocorrer o erro `413 Payload Too Large`.

Isso significa que existe uma diferença importante entre:

**tamanho máximo de uma requisição**

e

**tamanho máximo de um arquivo que pode ser enviado em várias requisições**.

Foi essa diferença que abriu o caminho para a solução utilizada posteriormente com o FileBrowser Quantum.

---

## 5. O que aprendemos

A principal lição desta etapa foi simples:

> Não basta o aplicativo funcionar dentro do Docker.

É preciso testar o caminho completo que será utilizado no dia a dia.

No caso deste projeto, isso significava testar:

- acesso local;
- acesso externo;
- login;
- navegação;
- download;
- upload;
- arquivos grandes;
- comportamento depois de reiniciar o container.

Os capítulos seguintes mostram como o projeto foi mudando a partir desses problemas.
