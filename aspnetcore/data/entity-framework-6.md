---
title: Introdução ao ASP.NET Core e ao Entity Framework 6
author: rick-anderson
description: Este artigo mostra como usar o Entity Framework 6 em um aplicativo ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 50f51a72341cbad017872df581da214211b9920a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886931"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a>Introdução ao ASP.NET Core e ao Entity Framework 6

Por [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) e [Tom Dykstra](https://github.com/tdykstra)

Este artigo mostra como usar o Entity Framework 6 em um aplicativo ASP.NET Core.

## <a name="overview"></a>Visão geral

Para usar o Entity Framework 6, o projeto precisa ser compilado no .NET Framework, pois o Entity Framework 6 não dá suporte ao .NET Core. Caso precise de recursos de multiplataforma, faça upgrade para o [Entity Framework Core](/ef/).

A maneira recomendada de usar o Entity Framework 6 em um aplicativo ASP.NET Core é colocar o contexto e as classes de modelo do EF6 em um projeto de biblioteca de classes direcionado à estrutura completa. Adicione uma referência à biblioteca de classes do projeto ASP.NET Core. Consulte a [solução de exemplo do Visual Studio com projetos EF6 e ASP.NET Core](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).

Não é possível colocar um contexto do EF6 em um projeto ASP.NET Core, pois projetos .NET Core não dão suporte a todas as funcionalidades exigidas pelo EF6, como *Enable-Migrations*, que é obrigatória.

Seja qual for o tipo de projeto em que você localize o contexto do EF6, somente ferramentas de linha de comando do EF6 funcionam com um contexto do EF6. Por exemplo, `Scaffold-DbContext` está disponível apenas no Entity Framework Core. Caso precise fazer engenharia reversa de um banco de dados para um modelo do EF6, consulte [Usar o Code First para um banco de dados existente](https://msdn.microsoft.com/jj200620).

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Referenciar a estrutura completa e o EF6 no projeto ASP.NET Core

O projeto ASP.NET Core precisa referenciar a estrutura .NET e o EF6. Por exemplo, o arquivo *.csproj* do projeto ASP.NET Core será semelhante ao exemplo a seguir (somente as partes relevantes do arquivo são mostradas).

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

Ao criar um novo projeto, use o modelo **Aplicativo Web ASP.NET Core (.NET Framework)**.

## <a name="handle-connection-strings"></a>Manipular as cadeias de conexão

As ferramentas de linha de comando do EF6 que você usará no projeto de biblioteca de classes do EF6 exigem um construtor padrão para que possam criar uma instância do contexto. No entanto, provavelmente, você desejará especificar a cadeia de conexão a ser usada no projeto ASP.NET Core, caso em que o construtor de contexto deve ter um parâmetro que permita passar a cadeia de conexão. Veja um exemplo.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

Como o contexto do EF6 não tem um construtor sem parâmetros, o projeto do EF6 precisa fornecer uma implementação de [IDbContextFactory](https://msdn.microsoft.com/library/hh506876). As ferramentas de linha de comando do EF6 encontrarão e usarão essa implementação para que possam criar uma instância do contexto. Veja um exemplo.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

Nesse código de exemplo, a implementação `IDbContextFactory` passa uma cadeia de conexão embutida em código. Essa é a cadeia de conexão que será usada pelas ferramentas de linha de comando. Recomendamos implementar uma estratégia para garantir que a biblioteca de classes use a mesma cadeia de conexão usada pelo aplicativo de chamada. Por exemplo, você pode obter o valor de uma variável de ambiente em ambos os projetos.

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Configurar a injeção de dependência no projeto ASP.NET Core

No arquivo *Startup.cs* do projeto Core, configure o contexto do EF6 para DI (injeção de dependência) em `ConfigureServices`. Os objetos de contexto do EF devem ser delimitados em escopo a um tempo de vida por solicitação.

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

Em seguida, você pode obter uma instância do contexto nos controladores usando a DI. O código é semelhante ao que você escreverá para um contexto do EF Core:

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>Aplicativo de exemplo

Para obter um aplicativo de exemplo funcional, consulte a [solução de exemplo do Visual Studio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) que acompanha este artigo.

Esta amostra pode ser criada do zero pelas seguintes etapas no Visual Studio:

* Crie uma solução.

* **Adicionar** > **Novo Projeto** > **Web** > **Aplicativo Web ASP.NET Core**
  * Na caixa de diálogo de seleção de modelo do projeto, selecione API e .NET Framework na lista suspensa

* **Adicionar** > **Novo Projeto** > **Windows Desktop** > **Biblioteca de Classes (.NET Framework)**

* No **PMC** (Console do Gerenciador de Pacotes) dos dois projetos, execute o comando `Install-Package Entityframework`.

* No projeto de biblioteca de classes, crie classes de modelo de dados e uma classe de contexto, bem como uma implementação de `IDbContextFactory`.

* No PMC do projeto de biblioteca de classes, execute os comandos `Enable-Migrations` e `Add-Migration Initial`. Caso tenha definido o projeto ASP.NET Core como o projeto de inicialização, adicione `-StartupProjectName EF6` a esses comandos.

* No projeto Core, adicione uma referência de projeto ao projeto de biblioteca de classes.

* No projeto Core, em *Startup.cs*, registre o contexto para DI.

* No projeto Core, em *appsettings.json*, adicione a cadeia de conexão.

* No projeto Core, adicione um controlador e exibições para verificar se é possível ler e gravar dados. (Observe que o scaffolding do ASP.NET Core MVC não funcionará com o contexto do EF6 referenciado da biblioteca de classes.)

## <a name="summary"></a>Resumo

Este artigo forneceu diretrizes básicas para usar o Entity Framework 6 em um aplicativo ASP.NET Core.

## <a name="additional-resources"></a>Recursos adicionais

* [Entity Framework – configuração baseada em código](https://msdn.microsoft.com/data/jj680699.aspx)
