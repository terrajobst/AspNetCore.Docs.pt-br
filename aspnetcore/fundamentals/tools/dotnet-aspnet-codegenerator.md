---
title: comando dotnet aspnet-codegenerator
author: rick-anderson
description: O comando dotnet aspnet-codegenerator aplica scaffold em projetos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665182"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator` – executa o mecanismo de scaffolding do ASP.NET Core. `dotnet aspnet-codegenerator` é necessário somente para fazer scaffold da linha de comando, não é preciso usar o scaffolding com o Visual Studio.

Este artigo se aplica ao [SDK do .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) e posteriores.

## <a name="installing-aspnet-codegenerator"></a>Instalação do aspnet-codegenerator

`dotnet-aspnet-codegenerator` é uma [ferramenta global](/dotnet/core/tools/global-tools) que deve ser instalada. O comando a seguir instala a versão estável mais recente da ferramenta `dotnet-aspnet-codegenerator`:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

O comando a seguir atualiza `dotnet-aspnet-codegenerator` para a versão estável mais recente disponível dos SDKs do .NET Core instalado:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Sinopse

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Descrição

O comando global `dotnet aspnet-codegenerator` executa o mecanismo de scaffolding e o gerador de código do ASP.NET Core.

## <a name="arguments"></a>Argumentos

`generator`

O gerador de código para ser executado. Os geradores a seguir estão disponíveis:

| Gerador | Operação |
| ----------------- | ------------ | 
| área      | [Faz scaffold de uma área](/aspnet/core/mvc/controllers/areas) |
  controlador| [Faz scaffold de um controlador](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identidade  | [Faz scaffold de uma identidade](/aspnet/core/security/authentication/scaffold-identity) |
  razorpage | [Faz scaffold de Razor Pages](/aspnet/core/tutorials/razor-pages/model) |
  modo de exibição      | [Faz scaffolds de um modo de exibição](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>{1&gt;Opções&lt;1}

`-n|--nuget-package-dir`

Especifica o diretório de pacote do NuGet.

`-c|--configuration {Debug|Release}`

Define a configuração da compilação. O valor padrão é `Debug`.

`-tfm|--target-framework`

O [Framework](/dotnet/standard/frameworks) destino para usar. Por exemplo, `net46`.

`-b|--build-base-path`

O caminho base da compilação.

`-h|--help`

Imprime uma ajuda breve para o comando.

`--no-build`

Não compila o projeto antes da execução. Também define o sinalizador `--no-restore` implicitamente.

`-p|--project <PATH>`

Especifica o caminho do arquivo de projeto a ser executado (nome da pasta ou caminho completo). Se não é especificado, ele usa como padrão o diretório atual.

## <a name="generator-options"></a>Opções de gerador

As seções a seguir detalham as opções disponíveis para os geradores com suporte:

* Área
* Controlador
* Identity  
* Razorpage
* Exibir

<a name="area"></a>

### <a name="area-options"></a>Opções de área

Essa ferramenta destina-se aos projetos Web do ASP.NET Core com controladores e exibições. Ele não é destinado a aplicativos de Razor Pages.

Uso: `dotnet aspnet-codegenerator area AreaNameToGenerate`

O comando anterior gera as seguintes pastas:

* *Áreas*
  * *AreaNameToGenerate*
    * *Controladores*
    * *Dados*
    * *Modelos*
    * *Exibições*

<a name="ctl"></a>

### <a name="controller-options"></a>Opções de controlador

A tabela a seguir lista as opções para `aspnet-codegenerator` `controller` e `razorpage`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

A tabela a seguir lista as opções exclusivas para `aspnet-codegenerator controller`:

| {1&gt;Opção&lt;1}               | Descrição|
| ----------------- | ------------ |
| --controllerName ou -name | O nome do controlador. |
| --useAsyncActions ou -async | Gera ações do controlador assíncrono. |
| --noViews ou -nv | **Não** gera modos de exibição. |
| --restWithNoViews ou -api  | Gera um controlador com API estilo REST. É assumido `noViews` e todas as opções relacionadas à visualização são ignoradas. |
| --readWriteActions ou -actions | Gere o controlador com ações de leitura/gravação sem um modelo. |

Use o switch `-h` para obter ajuda sobre o comando `aspnet-codegenerator controller`:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Confira [Fazer scaffold do modelo de filme](/aspnet/core/tutorials/razor-pages/model) para obter um exemplo de `dotnet aspnet-codegenerator controller`.

### <a name="razorpage"></a>Razorpage

<a name="rp"></a>

As Razor Pages podem ser geradas por scaffolding individualmente, especificando o nome da nova página e o modelo a ser usado. Os modelos com suporte são:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Por exemplo, o comando a seguir usa o modelo Editar para gerar *MyEdit.cshtml* e *MyEdit.cshtml.cs*:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Normalmente, o modelo e o nome de arquivo gerado não são especificados e os seguintes modelos são criados:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

A tabela a seguir lista as opções para `aspnet-codegenerator` `razorpage` e `controller`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

A tabela a seguir lista as opções exclusivas para `aspnet-codegenerator razorpage`:

| {1&gt;Opção&lt;1}               | Descrição|
| ----------------- | ------------ |
|   --namespaceName ou -namespace | O nome do namespace a ser usado no PageModel gerado |
| --partialView ou -partial | Gere uma exibição parcial. As opções de layout -l e - udl são ignoradas, se isso for especificado. |
| --noPageModel ou -npm | Alterne para não gerar uma classe PageModel para o modelo Vazio |

Use o switch `-h` para obter ajuda sobre o comando `aspnet-codegenerator razorpage`:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Confira [Fazer scaffold do modelo de filme](/aspnet/core/tutorials/razor-pages/model) para obter um exemplo de `dotnet aspnet-codegenerator razorpage`.

### <a name="identity"></a>Identity

Confira [Identidade Scaffold](/aspnet/core/security/authentication/scaffold-identity)
