---
title: Adicionar, baixar e excluir dados de usuário para identidade em um projeto ASP.NET Core
author: rick-anderson
description: Saiba como adicionar dados de usuário personalizados à identidade em um projeto ASP.NET Core. Excluir dados por GDPR.
ms.author: riande
ms.date: 06/18/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 6daca5776930f80eec8d81132b5a5c4d4d5c13ad
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681156"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Adicionar, baixar e excluir dados de usuário personalizados para identidade em um projeto ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este artigo mostra como:

* Adicione dados de usuário personalizados a um aplicativo Web ASP.NET Core.
* Decorar o modelo de dados de usuário personalizado com o atributo <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> para que ele seja automaticamente disponibilizado para download e exclusão. Tornar os dados capazes de ser baixados e excluídos ajuda a atender aos requisitos de [GDPR](xref:security/gdpr) .

O exemplo de projeto é criado de um aplicativo Web Razor Pages, mas as instruções são semelhantes para um aplicativo Web ASP.NET Core MVC.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Criar um aplicativo Web do Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**. Nomeie o projeto **WebApp1** se você quiser que ele corresponda ao namespace do código de [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Selecione **ASP.NET Core aplicativo Web** > **OK**
* Selecione **ASP.NET Core 3,0** na lista suspensa
* Selecione o **aplicativo Web** > **OK**
* Compile e execute o projeto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**. Nomeie o projeto **WebApp1** se você quiser que ele corresponda ao namespace do código de [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Selecione **ASP.NET Core aplicativo Web** > **OK**
* Selecione **ASP.NET Core 2,2** na lista suspensa
* Selecione o **aplicativo Web** > **OK**
* Compile e execute o projeto.

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Executar a identidade scaffolder

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.
* No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.
* Na caixa de diálogo **Adicionar identidade** , as seguintes opções:
  * Selecione o arquivo de layout existente *~/Pages/Shared/_Layout. cshtml*
  * Selecione os seguintes arquivos a serem substituídos:
    * **Conta/registro**
    * **Conta/gerenciamento/índice**
  * Selecione o botão **+** para criar uma nova **classe de contexto de dados**. Aceite o tipo (**WebApp1. Models. WebApp1Context** se o projeto for denominado **WebApp1**).
  * Selecione o botão **+** para criar uma nova **classe de usuário**. Aceite o tipo (**WebApp1User** se o projeto for nomeado **WebApp1**) > **Adicionar**.
* Selecione **Adicionar**.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (. csproj). Execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Execute o seguinte comando para listar as opções de scaffolder de identidade:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Na pasta do projeto, execute a identidade scaffolder:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Siga as instruções em [Migrations, UseAuthentication e layout](xref:security/authentication/scaffold-identity#efm) para executar as seguintes etapas:

* Crie uma migração e atualize o banco de dados.
* Adicione `UseAuthentication` a `Startup.Configure`.
* Adicione `<partial name="_LoginPartial" />` ao arquivo de layout.
* Teste o aplicativo:
  * Registrar um usuário
  * Selecione o novo nome de usuário (ao lado do link de **logout** ). Talvez seja necessário expandir a janela ou selecionar o ícone da barra de navegação para mostrar o nome de usuário e outros links.
  * Selecione a guia **dados pessoais** .
  * Selecione o botão **baixar** e examinou o arquivo *PersonalData. JSON* .
  * Teste o botão **excluir** , que exclui o usuário conectado.

## <a name="add-custom-user-data-to-the-identity-db"></a>Adicionar dados de usuário personalizados ao BD de identidade

Atualize a classe derivada `IdentityUser` com propriedades personalizadas. Se você tiver nomeado o projeto WebApp1, o arquivo será denominado *areas/Identity/data/WebApp1User. cs*. Atualize o arquivo com o seguinte código:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Propriedades decoradas com o atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) são:

* Excluído quando a página de *áreas/identidade/páginas/conta/gerenciamento/DeletePersonalData. cshtml* do Razor chama `UserManager.Delete`.
* Incluídos nos dados baixados pela página do Razor *áreas/identidade/páginas/conta/gerenciamento/DownloadPersonalData. cshtml* .

### <a name="update-the-accountmanageindexcshtml-page"></a>Atualizar a página conta/gerenciar/index. cshtml

Atualize o `InputModel` em *áreas/identidade/páginas/conta/gerenciamento/índice. cshtml. cs* com o seguinte código realçado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Atualize as *áreas/identidade/páginas/conta/gerenciamento/index. cshtml* com a seguinte marcação realçada:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Atualize as *áreas/identidade/páginas/conta/gerenciamento/index. cshtml* com a seguinte marcação realçada:

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Atualizar a página Account/Register. cshtml

Atualize o `InputModel` em *áreas/identidade/páginas/conta/Register. cshtml. cs* com o seguinte código realçado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Atualize as *áreas/identidade/páginas/conta/Register. cshtml* com a seguinte marcação realçada:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Atualize as *áreas/identidade/páginas/conta/Register. cshtml* com a seguinte marcação realçada:

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Crie o projeto.

### <a name="add-a-migration-for-the-custom-user-data"></a>Adicionar uma migração para os dados de usuário personalizados

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No **console do Gerenciador de pacotes**do Visual Studio:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Testar criar, exibir, baixar, excluir dados de usuário personalizados

Teste o aplicativo:

* Registrar um novo usuário.
* Exiba os dados de usuário personalizados na página `/Identity/Account/Manage`.
* Baixe e exiba os dados pessoais dos usuários na página `/Identity/Account/Manage/PersonalData`.
