# Manutenção e o que evitar

## O princípio mais importante

Depois que um serviço funciona, existe uma tentação de continuar mexendo nele.

Nem sempre é necessário.

Uma configuração boa é aquela que resolve o problema sem criar outros dez.

Por isso, a manutenção do FileBrowser Quantum deve seguir uma regra simples:

> **Se está funcionando, documente antes de alterar.**

---

## Antes de atualizar

Antes de mudar a imagem Docker ou o arquivo de configuração:

1. faça uma cópia dos arquivos importantes;
2. registre a versão que está funcionando;
3. anote o que será alterado;
4. faça a alteração;
5. teste;
6. se algo quebrar, volte para a configuração anterior.

---

## Não alterar várias coisas ao mesmo tempo

Se mudar:

- imagem;
- configuração;
- Cloudflare;
- volumes;
- permissões;

tudo de uma vez, fica difícil descobrir qual alteração causou o problema.

É melhor alterar uma coisa por vez quando o problema não exige uma mudança conjunta.

---

## Não copiar caminhos de outro servidor

Um caminho como:

```text
/mnt/servidor/arquivos
```

pode fazer sentido em uma instalação e não existir em outra.

Por isso, os exemplos deste repositório usam caminhos genéricos.

Quem for reproduzir a instalação deve adaptar os caminhos ao próprio servidor.

---

## Não publicar segredos

Nunca coloque no GitHub:

- senha;
- token;
- chave privada;
- cookie;
- credencial;
- arquivo de banco de dados;
- configuração privada do Cloudflare.

Se algo for necessário para reproduzir a configuração, substitua por um exemplo.

---

## Testar depois de qualquer mudança

O teste básico deve incluir:

```text
[ ] abrir o serviço
[ ] fazer login
[ ] navegar pelas pastas
[ ] baixar um arquivo
[ ] enviar um arquivo pequeno
[ ] enviar um arquivo grande
[ ] verificar o arquivo no servidor
[ ] reiniciar o container
[ ] testar novamente
```

O objetivo não é criar uma bateria de testes gigantesca.

É apenas garantir que as funções que realmente importam continuam funcionando.

---

## Cloudflare

Se o acesso externo estiver passando pelo Cloudflare, lembre que o limite de tamanho é aplicado por requisição.

Uploads grandes devem continuar utilizando a estratégia de envio em partes quando essa for a solução adotada pelo aplicativo.

Se o comportamento mudar depois de uma atualização, o primeiro lugar para olhar é a configuração de upload do Quantum e o caminho completo da requisição.

---

## O que evitar

Evite:

- atualizar sem registrar a versão anterior;
- copiar YAML sem entender os volumes;
- publicar configurações reais em um repositório público;
- alterar várias camadas simultaneamente durante troubleshooting;
- assumir que um erro do navegador necessariamente significa erro do container;
- assumir que um erro do Cloudflare necessariamente significa erro do FileBrowser;
- apagar o banco ou os volumes antes de ter backup.

---

## A documentação também faz parte da manutenção

Este repositório não serve apenas para ensinar outras pessoas.

Ele também serve como memória do próprio projeto.

Quando uma configuração for alterada, registre:

```text
o que mudou
por que mudou
o que deu errado
como foi corrigido
qual foi o resultado
```

Isso evita que uma solução descoberta hoje precise ser redescoberta daqui a alguns meses.

---

## Estado atual

O projeto atual está funcionando com o FileBrowser Quantum em Docker.

Os detalhes finais de configuração serão conferidos diretamente nos arquivos do servidor antes de serem publicados como exemplos.

A regra continua sendo a mesma:

> **Primeiro confirmar. Depois documentar.**
