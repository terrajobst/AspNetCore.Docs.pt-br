---
title: Usar os analisadores da API Web
author: pranavkm
description: Saiba mais sobre os analisadores da API Web em Microsoft.AspNetCore.Mvc.Api.Analyzers.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/14/2018
uid: web-api/advanced/analyzers
ms.openlocfilehash: 2aaef738ab2a64f85cb85708f63d2375c04cacb5
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538562"
---
# <a name="use-web-api-analyzers"></a>Usar os analisadores da API Web

O ASP.NET Core 2.2 (e posterior) inclui o pacote NuGet [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) contendo analisadores para APIs Web. Os analisadores trabalham com controladores anotados com <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> enquanto fazem a compilação em [convenções de API](xref:web-api/advanced/conventions).

## <a name="package-installation"></a>Instalação do pacote

`Microsoft.AspNetCore.Mvc.Api.Analyzers` pode ser adicionado com uma das seguintes abordagens:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Da janela **Console do Gerenciador de Pacotes**:
  * Acesse **Exibição** > **Outras Janelas** > **Console do Gerenciador de Pacotes**.
  * Navegue até o diretório no qual o arquivo *ApiConventions.csproj* está localizado.
  * Execute o seguinte comando:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

* Da caixa de diálogo **Gerenciar Pacotes NuGet**:
  * Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**.
  * Defina a **Origem do pacote** como "nuget.org".
  * Insira "Microsoft.AspNetCore.Mvc.Api.Analyzers" na caixa de pesquisa.
  * Selecione o pacote "Microsoft.AspNetCore.Mvc.Api.Analyzers" na guia **Procurar** e clique em **Instalar**.

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse na pasta *Pacotes* em **Painel de Soluções** > **Adicionar Pacotes...** .
* Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** como "nuget.org".
* Insira "Microsoft.AspNetCore.Mvc.Api.Analyzers" na caixa de pesquisa.
* Selecione o pacote "Microsoft.AspNetCore.Mvc.Api.Analyzers" do painel de resultados e clique em **Adicionar Pacote**.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o comando a seguir do **Terminal Integrado**:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Execute o seguinte comando:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

## <a name="analyzers-for-api-conventions"></a>Analisadores para convenções de API

Documentos de OpenAPI contêm códigos de status e tipos de resposta que uma ação pode retornar. No ASP.NET Core MVC, atributos como <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> e <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> são usados para documentar uma ação. <xref:tutorials/web-api-help-pages-using-swagger> apresenta mais detalhes sobre como documentar sua API.

Um dos analisadores no pacote inspeciona controladores anotados com <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> e identifica ações que não documentam totalmente as respostas. Considere o exemplo a seguir:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=9)]

A ação precedente documenta o tipo de retorno com êxito do HTTP 200, mas não documenta o código de status com falha do HTTP 404. O analisador relata a documentação ausente para o código de status HTTP 404 como um aviso. É fornecida uma opção para consertar o problema.

![analisador relatando um aviso](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
