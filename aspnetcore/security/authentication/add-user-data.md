---
title: Adicionar, baixar e excluir dados de usuário à identidade em um projeto ASP.NET Core
author: rick-anderson
description: Saiba como adicionar dados personalizados do usuário a identidade em um projeto ASP.NET Core. Exclua dados por GDPR.
ms.author: riande
ms.date: 12/05/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: f54df68834cd3e2493e558aaab9851f036f3f01b
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880751"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Adicionar, baixar e excluir dados de usuário personalizada à identidade em um projeto ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este artigo mostra como:

* Adicione dados de usuário personalizada para um aplicativo web ASP.NET Core.
* Marque o modelo de dados de usuário personalizado com o atributo <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> para que ele seja automaticamente disponibilizado para download e exclusão. Tornando os dados capazes de ser baixado e excluído ajuda a cumprir [GDPR](xref:security/gdpr) requisitos.

O exemplo de projeto é criado a partir de um aplicativo web páginas Razor, mas as instruções são semelhantes para um aplicativo web ASP.NET Core MVC.

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

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**. Nomeie o projeto **WebApp1** se você deseja corresponder ao namespace da [baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) código.
* Selecione **ASP.NET Core aplicativo Web** > **OK**
* Selecione **ASP.NET Core 3,0** na lista suspensa
* Selecione o **aplicativo Web** > **OK**
* Compile e execute o projeto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**. Nomeie o projeto **WebApp1** se você deseja corresponder ao namespace da [baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) código.
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

## <a name="run-the-identity-scaffolder"></a>Execute o scaffolder de identidade

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Partir **Gerenciador de soluções**, clique com botão direito no projeto > **Add** > **New Scaffolded Item**.
* No painel à esquerda do **adicionar Scaffold** caixa de diálogo, selecione **identidade** > **adicionar**.
* No **identidade de adição** caixa de diálogo, as seguintes opções:
  * Selecione o arquivo de layout existente *~/Pages/Shared/_Layout.cshtml*
  * Selecione os arquivos a seguir para substituir:
    * **Conta/registro**
    * **Conta/gerenciar/índice**
  * Selecione o **+** botão para criar um novo **classe de contexto de dados**. Aceite o tipo (**WebApp1.Models.WebApp1Context** se o projeto é denominado **WebApp1**).
  * Selecione o **+** botão para criar um novo **classe User**. Aceite o tipo (**WebApp1User** se o projeto é denominado **WebApp1**) > **adicionar**.
* Selecione **adicionar**.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione uma referência de pacote ao [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (. csproj). Execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Execute o seguinte comando para listar as opções de scaffolder de identidade:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Na pasta do projeto, execute o scaffolder de identidade:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Siga as instruções da [migrações, UseAuthentication e layout](xref:security/authentication/scaffold-identity#efm) para executar as seguintes etapas:

* Criar uma migração e atualizar o banco de dados.
* Adicione `UseAuthentication` a `Startup.Configure`.
* Adicionar `<partial name="_LoginPartial" />` ao arquivo de layout.
* Teste o aplicativo:
  * Registrar um usuário
  * Selecione o novo nome de usuário (ao lado de **Logout** link). Talvez você precise expandir a janela ou selecione o ícone da barra de navegação para mostrar o nome de usuário e outros links.
  * Selecione o **dados pessoais** guia.
  * Selecione o **Baixe** botão e examinado o *PersonalData.json* arquivo.
  * Teste o **excluir** botão, que exclui o fez logon de usuário.

## <a name="add-custom-user-data-to-the-identity-db"></a>Adicionar dados de usuário personalizada para o banco de dados de identidade

Atualização de `IdentityUser` derivado da classe com propriedades personalizadas. Se você nomeou o projeto WebApp1, o arquivo é nomeado *Areas/Identity/Data/WebApp1User.cs*. Atualize o arquivo com o código a seguir:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

As propriedades com o atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) são:

* Excluído, quando o *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* página do Razor chama `UserManager.Delete`.
* Incluído nos dados baixados pela *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* página do Razor.

### <a name="update-the-accountmanageindexcshtml-page"></a>Atualizar a página Account/Manage/Index.cshtml

Atualizar o `InputModel` na *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* com o seguinte código realçado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Atualizar o *Areas/Identity/Pages/Account/Manage/Index.cshtml* com a seguinte marcação realçada:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Atualizar o *Areas/Identity/Pages/Account/Manage/Index.cshtml* com a seguinte marcação realçada:

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Atualizar a página Register

Atualizar o `InputModel` na *Areas/Identity/Pages/Account/Register.cshtml.cs* com o seguinte código realçado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Atualizar o *Areas/Identity/Pages/Account/Register.cshtml* com a seguinte marcação realçada:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Atualizar o *Areas/Identity/Pages/Account/Register.cshtml* com a seguinte marcação realçada:

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Crie o projeto.

### <a name="add-a-migration-for-the-custom-user-data"></a>Adicionar uma migração para os dados de usuário personalizada

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No Visual Studio **Package Manager Console**:

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

## <a name="test-create-view-download-delete-custom-user-data"></a>Teste de criar, exibir, baixar, excluir dados de usuário personalizada

Teste o aplicativo:

* Registre um novo usuário.
* Exibir os dados de usuário personalizada no `/Identity/Account/Manage` página.
* Baixar e exibir os dados pessoais de usuários da `/Identity/Account/Manage/PersonalData` página.
