---
title: Exemplos de autenticação para ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b013d02a65e752bbb61a87a0bf502785125378a2
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511607"
---
# <a name="authentication-samples-for-aspnet-core"></a>Exemplos de autenticação para ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :

* [Transformação de declarações](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [Autenticação de cookie](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [Provedor de política personalizada-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [Opções e esquemas de autenticação dinâmica](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [Declarações externas](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [Seleção entre o cookie e outro esquema de autenticação com base na solicitação](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [Restringe o acesso a arquivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Executar as amostras

* Selecione uma [ramificação](https://github.com/dotnet/AspNetCore). Por exemplo, `Tag:v3.0.0`
* Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.
* Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo com `dotnet run`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :

* [Transformação de declarações](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Autenticação de cookie](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Provedor de política personalizada-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Opções e esquemas de autenticação dinâmica](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Declarações externas](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Seleção entre o cookie e outro esquema de autenticação com base na solicitação](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restringe o acesso a arquivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Executar as amostras

* Selecione uma [ramificação](https://github.com/dotnet/AspNetCore). Por exemplo, `release/2.2`
* Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.
* Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo com `dotnet run`.

::: moniker-end
