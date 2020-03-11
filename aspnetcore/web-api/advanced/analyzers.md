---
title: Usar os analisadores da API Web
author: pranavkm
description: Saiba mais sobre o ASP.NET Core pacote de analisadores de API Web MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
uid: web-api/advanced/analyzers
ms.openlocfilehash: 7b6a7328deb8718a2a1c67c104cec359a4f13497
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661913"
---
# <a name="use-web-api-analyzers"></a>Usar os analisadores da API Web

O ASP.NET Core 2,2 e posterior fornece um pacote de analisadores MVC destinado ao uso com projetos de API Web. Os analisadores trabalham com controladores anotados com <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, ao criar [convenções de API Web](xref:web-api/advanced/conventions).

O pacote de analisadores notifica você sobre qualquer ação do controlador que:

* Retorna um código de status não declarado.
* Retorna um resultado de êxito não declarado.
* Documenta um código de status que não é retornado.
* Inclui uma verificação de validação de modelo explícita.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Referenciar o pacote do analisador

No ASP.NET Core 3,0 ou posterior, os analisadores estão incluídos na SDK do .NET Core. Para habilitar o analisador em seu projeto, inclua a propriedade `IncludeOpenAPIAnalyzers` no arquivo de projeto:

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Instalação do pacote

Instale o pacote NuGet [Microsoft. AspNetCore. Mvc. API. Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) com uma das seguintes abordagens:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Da janela **Console do Gerenciador de Pacotes**:
  * Acesse **exibir** > **outro** console do **gerenciador de pacotes**do Windows >.
  * Navegue até o diretório no qual o arquivo *ApiConventions.csproj* está localizado.
  * Execute o comando a seguir:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse na pasta *pacotes* em **painel de soluções** > **adicionar pacotes...** .
* Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** como "nuget.org".
* Insira "Microsoft.AspNetCore.Mvc.Api.Analyzers" na caixa de pesquisa.
* Selecione o pacote "Microsoft.AspNetCore.Mvc.Api.Analyzers" do painel de resultados e clique em **Adicionar Pacote**.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o comando a seguir do **Terminal Integrado**:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Execute o comando a seguir:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Analisadores para convenções de API Web

Documentos de OpenAPI contêm códigos de status e tipos de resposta que uma ação pode retornar. No ASP.NET Core MVC, atributos como <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> e <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> são usados para documentar uma ação. <xref:tutorials/web-api-help-pages-using-swagger> entrará em mais detalhes sobre como documentar sua API Web.

Um dos analisadores no pacote inspeciona controladores anotados com <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> e identifica ações que não documentam totalmente as respostas. Considere o exemplo a seguir:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

A ação precedente documenta o tipo de retorno com êxito do HTTP 200, mas não documenta o código de status com falha do HTTP 404. O analisador relata a documentação ausente para o código de status HTTP 404 como um aviso. É fornecida uma opção para consertar o problema.

![analisador relatando um aviso](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
