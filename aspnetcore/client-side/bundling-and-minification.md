---
title: Agrupar e minificar ativos estáticos no ASP.NET Core
author: scottaddie
description: Aprenda a otimizar os recursos estáticos em um aplicativo web ASP.NET Core por meio da aplicação de técnicas de agrupamento e minificação.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: 6254a74fd0a11669706a2a89b156a3223e300d1c
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813508"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Agrupar e minificar ativos estáticos no ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie) e [Alcatrão de David](https://twitter.com/davidpine7)

Este artigo explica os benefícios da aplicação de agrupamento e minificação, incluindo como esses recursos podem ser usados com aplicativos web ASP.NET Core.

## <a name="what-is-bundling-and-minification"></a>O que é o agrupamento e minificação

Agrupamento e minificação são duas otimizações de desempenho distintos, que você pode aplicar em um aplicativo web. Usados juntos, agrupamento e minificação melhoram o desempenho reduzindo o número de solicitações do servidor e reduzindo o tamanho dos ativos mais solicitados estáticos.

Agrupamento e minificação basicamente melhoram o tempo de carregamento de solicitação de página primeiro. Depois que uma página da web foi solicitado, o navegador armazena em cache os recursos estáticos (JavaScript, CSS e imagens). Consequentemente, agrupamento e minificação não melhoram o desempenho ao solicitar a mesma página ou páginas, no mesmo site que está solicitando os mesmos recursos. Se a expira cabeçalho não está definido corretamente nos ativos e se não for usado o agrupamento e minificação, heurística de atualização do navegador marca os ativos obsoletos depois de alguns dias. Além disso, o navegador requer uma solicitação de validação para cada ativo. Nesse caso, o agrupamento e minificação fornecem uma melhoria de desempenho, mesmo após a primeira solicitação de página.

### <a name="bundling"></a>Agrupamento

O agrupamento combina vários arquivos em um único arquivo. Agrupamento reduz o número de solicitações de servidor que são necessários para renderizar um ativo da web, como uma página da web. Você pode criar qualquer número de pacotes individuais especificamente para o CSS, JavaScript, etc. Menos arquivos significa menos solicitações HTTP do navegador para o servidor ou do serviço de fornecimento de seu aplicativo. Isso resulta em melhor desempenho de carregamento de página primeiro.

### <a name="minification"></a>Minimização

Minificação remove caracteres desnecessários de código sem alterar a funcionalidade. O resultado é uma redução de tamanho significativo nos ativos mais solicitados (como CSS, imagens e arquivos JavaScript). Os efeitos colaterais de minimização incluem encurtar os nomes de variável para um caractere e remover comentários e espaço em branco desnecessário.

Considere a seguinte função de JavaScript:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minimização reduz a função para o seguinte:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Além de remover os comentários e espaço em branco desnecessário, os seguintes nomes de parâmetro e variável foram renomeados da seguinte maneira:

Original | Renomeado
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Impacto de agrupamento e minificação

A tabela a seguir descreve as diferenças entre carregar ativos individualmente e usando o agrupamento e minificação:

Ação | Com B/M | Sem B/M | Alteração
--- | :---: | :---: | :---:
Solicitações de arquivos  | 7   | 18     | 157%
KB transferido | 156 | 264.68 | 70%
Tempo de carregamento (ms) | 885 | 2360   | 167%

Navegadores são bastante detalhados em relação a cabeçalhos de solicitação HTTP. O total de bytes enviados métrica viu uma redução significativa ao agrupamento. O tempo de carregamento mostra uma melhoria significativa, no entanto, este exemplo foi executado localmente. Maior ganhos de desempenho são obtidos ao usar o agrupamento e minificação com ativos transferidos por uma rede.

## <a name="choose-a-bundling-and-minification-strategy"></a>Escolher uma estratégia de agrupamento e minificação

Os modelos de projeto do MVC e páginas Razor oferecem uma solução de out-of-the-box para agrupamento e minificação consiste em um arquivo de configuração JSON. Ferramentas de terceiros, tais como o [Grunt](xref:client-side/using-grunt) executor de tarefas, executar as mesmas tarefas com um pouco mais complexidade. Uma ferramenta de terceiros é uma excelente opção quando o fluxo de trabalho de desenvolvimento requer processamento além do agrupamento e minificação&mdash;como otimização linting e imagem. Usando o agrupamento e minificação tempo de design, os arquivos reduzidos são criados antes da implantação do aplicativo. Empacotando e minimizando antes da implantação tem a vantagem de carga do servidor reduzido. No entanto, é importante reconhecer que esse tempo de design de agrupamento e minificação aumenta a complexidade de build e só funciona com arquivos estáticos.

## <a name="configure-bundling-and-minification"></a>Configurar o agrupamento e minificação

::: moniker range="<= aspnetcore-2.0"

No ASP.NET Core 2.0 ou anterior, os modelos de projeto do MVC e páginas do Razor fornecem uma *bundleconfig.json* arquivo de configuração que define as opções para cada pacote:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

No ASP.NET Core 2.1 ou posterior, adicione um novo arquivo JSON, denominado *bundleconfig.json*, para a raiz do projeto MVC ou páginas do Razor. Inclua o JSON a seguir no arquivo como um ponto de partida:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

O *bundleconfig.json* arquivo define as opções para cada pacote. No exemplo anterior, uma configuração de pacote único é definida para o JavaScript personalizado (*wwwroot/js/site.js*) e a folha de estilos (*wwwroot/css/site.css*) arquivos.

Opções de configuração incluem:

* `outputFileName`: O nome do arquivo de pacote de saída. Pode conter um caminho relativo do *bundleconfig.json* arquivo. **required**
* `inputFiles`: Uma matriz de arquivos para agrupar. Esses são os caminhos relativos para o arquivo de configuração. **opcional**, * um valor vazio resulta em um arquivo de saída vazia. [recurso de curinga](https://www.tldp.org/LDP/abs/html/globbingref.html) padrões são suportados.
* `minify`: As opções de minimização para o tipo de saída. **optional**, *default - `minify: { enabled: true }`*
  * Opções de configuração estão disponíveis por tipo de arquivo de saída.
    * [Minificador CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minificador de JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minificador de HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Sinalizador que indica se deseja adicionar os arquivos gerados para o arquivo de projeto. **opcional**, *padrão – false*
* `sourceMap`: Sinalizador que indica se é necessário gerar um mapa de código-fonte para o arquivo agrupado. **opcional**, *padrão – false*
* `sourceMapRootPath`: O caminho raiz para armazenar o arquivo de mapa de código-fonte gerado.

## <a name="build-time-execution-of-bundling-and-minification"></a>Compilação em tempo de execução de agrupamento e minificação

O [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) pacote NuGet permite que a execução de agrupamento e minificação no momento da compilação. O pacote injeta [destinos do MSBuild](/visualstudio/msbuild/msbuild-targets) quais executar na compilação e de hora limpa. O *bundleconfig.json* arquivo é analisado pelo processo de compilação para produzir os arquivos de saída com base na configuração definida.

> [!NOTE]
> BuildBundlerMinifier pertence a um projeto voltado à comunidade no GitHub para o qual a Microsoft fornece sem suporte. Problemas devem ser arquivados [aqui](https://github.com/madskristensen/BundlerMinifier/issues).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Adicione a *BuildBundlerMinifier* pacote ao seu projeto.

Compile o projeto. O exemplo a seguir é exibida na janela de saída:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Limpe o projeto. O exemplo a seguir é exibida na janela de saída:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Adicione a *BuildBundlerMinifier* pacote ao seu projeto:

```console
dotnet add package BuildBundlerMinifier
```

Se usar o ASP.NET Core 1.x, restaure o pacote recém adicionado:

```console
dotnet restore
```

Compile o projeto:

```console
dotnet build
```

O exemplo a seguir será exibida:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Limpe o projeto:

```console
dotnet clean
```

A saída a seguir é exibida:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Execução ad hoc de agrupamento e minificação

É possível executar as tarefas de empacotamento e minimização em uma base ad hoc, sem compilar o projeto. Adicione a [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) pacote NuGet ao seu projeto:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core pertence a um projeto voltado à comunidade no GitHub para o qual a Microsoft fornece sem suporte. Problemas devem ser arquivados [aqui](https://github.com/madskristensen/BundlerMinifier/issues).

Este pacote estende a CLI do .NET Core para incluir a *pacote dotnet* ferramenta. O comando a seguir pode ser executado na janela do Console de Gerenciador de pacote (PMC) ou em um shell de comando:

```console
dotnet bundle
```

> [!IMPORTANT]
> Gerenciador de pacotes NuGet adiciona as dependências para o arquivo *. csproj como `<PackageReference />` nós. O `dotnet bundle` comando está registrado com a CLI do .NET Core somente quando um `<DotNetCliToolReference />` nó é usado. Modifique o arquivo *. csproj adequadamente.

## <a name="add-files-to-workflow"></a>Adicionar arquivos ao fluxo de trabalho

Considere um exemplo no qual adicional *Custom. CSS* arquivo for adicionado a seguir:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Para minificar *Custom. CSS* e agrupá-la com *CSS* em um *site* arquivo, adicione o caminho relativo para *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Como alternativa, é possível usar o seguinte padrão de recurso de curinga:
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> Esse padrão de recurso de curinga corresponde a todos os arquivos CSS e exclui o padrão de arquivo reduzido.

Compile o aplicativo. Abra *site* e observe o conteúdo da *Custom. CSS* é acrescentado ao final do arquivo.

## <a name="environment-based-bundling-and-minification"></a>Agrupamento e minificação com base no ambiente

Como prática recomendada, os arquivos agrupados e minificados do seu aplicativo devem ser usados em um ambiente de produção. Durante o desenvolvimento, os arquivos originais fazem para depuração mais fácil do aplicativo.

Especificar quais arquivos serão incluídos em suas páginas usando o [auxiliar de marca de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) em modos de exibição. O auxiliar de marca de ambiente renderiza apenas seu conteúdo ao executar em particular [ambientes](xref:fundamentals/environments).

O seguinte `environment` marca renderiza os arquivos CSS não processados durante a execução no `Development` ambiente:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

O seguinte `environment` marca renderiza os arquivos CSS agrupados e minificados quando em execução em um ambiente diferente de `Development`. Por exemplo, em execução no `Production` ou `Staging` dispara o processamento dessas folhas de estilo:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Consumir bundleconfig.json do Gulp

Há casos em que o fluxo de trabalho agrupamento e minificação do aplicativo requer processamento adicional. Exemplos incluem processamento de ativos da CDN, extrapolação de cache e otimização de imagem. Para atender a esses requisitos, você pode converter o fluxo de trabalho de agrupamento e minificação para usar o Gulp.

### <a name="use-the-bundler--minifier-extension"></a>Usar a extensão Bundler & Minifier

O Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extensão lida com a conversão para o Gulp.

> [!NOTE]
> A extensão Bundler & Minifier pertence a um projeto voltado à comunidade no GitHub para o qual a Microsoft fornece sem suporte. Problemas devem ser arquivados [aqui](https://github.com/madskristensen/BundlerMinifier/issues).

Clique com botão direito do *bundleconfig.json* arquivo no Gerenciador de soluções e selecione **Bundler & Minifier** > **converter Gulp...** :

![Converter para Gulp item de menu de contexto](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

O *gulpfile. js* e *Package. JSON* arquivos são adicionados ao projeto. O suporte a [npm](https://www.npmjs.com/) os pacotes listados na *Package. JSON* do arquivo `devDependencies` seção estão instalados.

Execute o seguinte comando na janela do PMC para instalar a CLI do Gulp como uma dependência global:

```console
npm i -g gulp-cli
```

O *gulpfile. js* leituras de arquivos a *bundleconfig.json* arquivo para as entradas, saídas e as configurações.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Converter manualmente

Se o Visual Studio e/ou a extensão Bundler & Minifier não estiverem disponível, converta manualmente.

Adicionar um *Package. JSON* arquivo pelo seguinte `devDependencies`, para a raiz do projeto:

> [!WARNING]
> O `gulp-uglify` módulo não dá suporte a ECMAScript (ES) 2015 / ES6 e versões posteriores. Instale [gulp terser](https://www.npmjs.com/package/gulp-terser) em vez de `gulp-uglify` usar ES2015 / ES6 ou posterior.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Instalar as dependências, executando o comando a seguir no mesmo nível que *Package. JSON*:

```console
npm i
```

Instale a CLI do Gulp como uma dependência global:

```console
npm i -g gulp-cli
```

Cópia de *gulpfile. js* abaixo o arquivo para a raiz do projeto:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Executar tarefas de Gulp

Para disparar a tarefa do Gulp minificação antes que o projeto é compilado no Visual Studio, adicione o seguinte [destino do MSBuild](/visualstudio/msbuild/msbuild-targets) para o arquivo *. csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

Neste exemplo, as tarefas definidas dentro de `MyPreCompileTarget` execução antes do predefinidos de destino `Build` destino. Saída semelhante à seguinte é exibida na janela de saída do Visual Studio:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```


## <a name="additional-resources"></a>Recursos adicionais

* [Usar o Grunt](xref:client-side/using-grunt)
* [Usar vários ambientes](xref:fundamentals/environments)
* [Auxiliares de marcação](xref:mvc/views/tag-helpers/intro)
