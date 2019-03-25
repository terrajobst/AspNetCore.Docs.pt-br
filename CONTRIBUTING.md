---
ms.openlocfilehash: 98a03118954baa85b093a0514e1ac6f0fb6353e8
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320102"
---
# <a name="contribute-to-the-aspnet-core-documentation"></a>Contribuir para a documentação do ASP.NET Core

Este documento aborda o processo para contribuir para os artigos e exemplos de código hospedados no [site de documentação do ASP.NET](https://docs.microsoft.com/aspnet/). Correções de erros de digitação e novos artigos são contribuições bem-vindas.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Como fazer uma correção ou sugestão simples

Artigos são armazenados no repositório, como arquivos de Markdown. São feitas alterações simples ao conteúdo de um arquivo Markdown no navegador selecionando o link **Editar** no canto superior direito da janela do navegador. (Em uma janela de navegador estreita, expanda a barra **Opções** para ver o link **Editar**.) Siga as instruções para criar uma PR (solicitação de pull). Vamos examinar a PR e aceitá-la ou sugerir alterações.

## <a name="how-to-make-a-more-complex-submission"></a>Como fazer um envio mais complexo

Você precisa de uma compreensão básica do [Git e do GitHub.com](https://guides.github.com/activities/hello-world/).

* Abra um [Problema](https://github.com/aspnet/Docs/issues/new) descrevendo o que você deseja fazer, como alterar um artigo existente ou criar um novo. Geralmente, solicitamos uma estrutura de tópicos para uma nova sugestão de tópico. Aguarde a aprovação da equipe antes de investir muito tempo.
* Bifurque o repositório [aspnet/Docs](https://github.com/aspnet/Docs/) e crie uma ramificação para suas alterações.
* Enviar um PR para mestre com suas alterações.
* Se a PR tiver o rótulo 'cla-required' atribuído, [conclua o CLA (Contrato de Licença de Contribuição)](https://cla.dotnetfoundation.org/).
* Responda aos comentários de PR.

Para obter um exemplo em que esse processo levou à publicação de um novo artigo, confira [Problema &num;67](https://github.com/dotnet/docs/issues/67) e [Solicitação Pull &num;798](https://github.com/dotnet/docs/pull/798) no repositório de Documentos do .NET. O novo artigo é [Documentando seu código](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Extensão do Pacote de Criação de Documentos no Visual Studio Code

Se você usar o Visual Studio Code para contribuir para a documentação do ASP.NET, você poderá aumentar sua produtividade instalando a extensão [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack). A extensão fornece uma variedade de ferramentas que ajudam com linting de Markdown, verificação ortográfica do código e modelos de artigo.

## <a name="markdown-syntax"></a>Sintaxe de markdown

Os artigos são escritos em [Markdown para DocFx](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), que é um superconjunto de [GFM (Markdown para GitHub)](https://guides.github.com/features/mastering-markdown/). Para obter exemplos de sintaxe do DFM para recursos de interface do usuário comumente usados na documentação do ASP.NET, confira [Metadados e Modelo de Markdown](https://github.com/dotnet/docs/blob/master/styleguide/template.md) no guia de estilo do repositório de Documentos do .NET. 

## <a name="folder-structure-conventions"></a>Convenções de estrutura de pasta

Para cada arquivo de Markdown, podem existir uma pasta para imagens e uma pasta para o código de exemplo. Se o artigo for [fundamentals/configuration/index.md](https://github.com/aspnet/Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), as imagens estarão em [fundamentals/configuration/index/\_static](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) e os arquivos de projeto do aplicativo de exemplo estarão em [fundamentals/configuration/index/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample). Uma imagem no arquivo *fundamentals/configuration/index.md* é processado pelo seguinte Markdown:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Todas as imagens devem ter um [texto alternativo (alt)](https://wikipedia.org/wiki/Alt_attribute). Para obter orientação sobre como especificar texto alternativo, confira recursos online, como [WebAIM: texto alternativo](https://webaim.org/techniques/alttext/).

Use letras minúsculas para nomes de arquivo de Markdown e nomes de arquivo de imagem.

## <a name="internal-links"></a>Links internos

Links internos devem usar o `uid` do artigo de destino com um link xref (o texto do link está definido como o título do conteúdo vinculado):

```md
<xref:uid_of_the_topic>
```

Se o título do artigo for inadequado para o texto do link (por exemplo, uma palavra ou frase em uma sentença for o texto do link), especifique o texto do link e o link xref com o seguinte:

```md
[link text](xref:uid_of_the_topic)
```

Para obter mais informações, confira a [Referência Cruzada do DocFX](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).

## <a name="images-and-screenshots"></a>Imagens e capturas de tela

Não inclua imagens nos artigos, exceto:

* Nos tutoriais básicos de integração (iniciante).
* Quando uma imagem é necessária para esclarecimento.

Essas restrições reduzem o tamanho do repositório.

Como uma etapa opcional, compacte as imagens e as capturas de tela usadas na documentação, o que ajuda com o desempenho de carregamento de página e o tamanho do arquivo. Algumas ferramentas populares incluem TinyPNG (usando o [site TinyPNG](https://tinypng.com/) ou a [API TinyPNG](https://tinypng.com/developers)) ou a extensão [Otimizador de Imagem](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) do Visual Studio. 

## <a name="code-snippets"></a>Snippets de código

Artigos geralmente contêm snippets de código para ilustrar os pontos. O DFM permite que você copie o código para o arquivo de Markdown ou consulte um arquivo de código separado. Preferimos usar arquivos de código separados sempre que possível para minimizar a chance de erros no código. Os arquivos de código são armazenados no repositório usando a estrutura de pastas descrita anteriormente para projetos de exemplo. 

Os exemplos a seguir ilustram a [sintaxe do snippet de código do DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) a ser usada em um arquivo *configuration/index.md*.

Para renderizar um arquivo de código inteiro como um snippet:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Para renderizar uma parte de um arquivo como um snippet usando números de linha:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

Para snippets C#, faça referência a uma [região do C#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). Sempre que possível, use regiões, em vez de números de linha, pois números de linha em um arquivo de código tendem a mudar e sair de sincronia com referências de número de linha em Markdown. Regiões do C# podem ser aninhadas. Se estiver fazendo referência à região externa, as diretivas `#region` e `#endregion` internas não serão renderizadas em um snippet. 

Para renderizar uma região C# chamada "snippet_Example":

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Para realçar as linhas selecionadas em um snippet renderizado (geralmente é renderizado como a cor da tela de fundo amarela):

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Testar alterações com o DocFX

Teste suas alterações com a [ferramenta de linha de comando do DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), que cria uma versão hospedada localmente do site. O DocFX não renderiza extensões de site e estilo criadas para docs.microsoft.com.

O DocFX requer:

* .NET framework no Windows.
* Mono para Linux ou macOS. 

### <a name="windows-instructions"></a>Instruções do Windows

* Baixe e descompacte *docfx.zip* de [versões do DocFX](https://github.com/dotnet/docfx/releases).
* Adicione DocFX ao seu PATH.
* Em um shell de comando, navegue até a pasta que contém o arquivo *docfx.json* (*aspnet* para conteúdo do ASP.NET ou *aspnetcore* para conteúdo do ASP.NET Core) e execute o seguinte comando:

  ```console
  docfx --serve
  ```

* Em um navegador, navegue até `http://localhost:8080/group1-dest/`.

### <a name="mono-instructions"></a>Instruções do Mono

* Instalar o Mono por meio do Homebrew:

  ```console
  brew install mono
  ```

* Baixe a [versão mais recente do DocFX](https://github.com/dotnet/docfx/releases).
* Extraia o arquivo para *$HOME/bin/docfx*.
* Crie um par de aliases para **docfx** em um shell de Busca. O primeiro alias é usado para criar a documentação. O segundo alias é usado para criar e fornecer a documentação.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* Em um shell de comando, navegue até a pasta que contém o arquivo *docfx.json* (*aspnet* para conteúdo do ASP.NET ou *aspnetcore* para conteúdo do ASP.NET Core) e execute o seguinte comando para criar e fornecer os documentos usando o respectivo alias:

  ```console
  docfx-serve
  ```

* Em um navegador, navegue até `http://localhost:8080/group1-dest/`.

## <a name="voice-and-tone"></a>Voz e tom

Nossa meta é escrever documentação que possa ser facilmente compreendida pelo maior público possível. Para esse fim, estabelecemos diretrizes para estilo de escrita que pedimos que nosso colaboradores sigam. Para obter mais informações, confira [Diretrizes de voz e tom](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) no repositório do .NET.

## <a name="microsoft-writing-style-guide"></a>Guia de Estilo de Escrita da Microsoft

O [Guia de Estilo de Escrita da Microsoft](https://docs.microsoft.com/style-guide/welcome/) fornece orientação sobre estilo e terminologia de escrita para todas as formas de comunicação de tecnologia, incluindo a documentação do ASP.NET Core.

## <a name="redirects"></a>Redirecionamentos

Se você excluir um artigo, altere seu nome de arquivo ou mova-o para uma pasta diferente, crie um redirecionamento para que as pessoas que marcaram o artigo como favorito não recebem um erro *404 Não Encontrado*. Adicione redirecionamentos para o [arquivo de redirecionamento mestre](https://github.com/aspnet/Docs/blob/master/.openpublishing.redirection.json).
