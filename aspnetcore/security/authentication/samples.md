---
title: Exemplos de autenticação para ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: efa177245faceddad4eb80de9e6f6d38e1a4261c
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022421"
---
# <a name="authentication-samples-for-aspnet-core"></a>Exemplos de autenticação para ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

O [repositório de ASP.NET Core](https://github.com/aspnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :

* [Transformação de declarações](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Autenticação de cookie](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Provedor de política personalizada-IAuthorizationPolicyProvider](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Opções e esquemas de autenticação dinâmica](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Declarações externas](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Seleção entre o cookie e outro esquema de autenticação com base na solicitação](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restringe o acesso a arquivos estáticos](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Executar os exemplos

* Selecione uma [ramificação](https://github.com/aspnet/AspNetCore). Por exemplo, `Tag:v3.0.0`
* Clone ou baixe o [repositório ASP.NET Core](https://github.com/aspnet/AspNetCore).
* Verifique se você instalou a versão [SDK do .NET Core](https://www.microsoft.com/net/download/all) correspondente ao clone do repositório ASP.NET Core.
* Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo `dotnet run`com.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O [repositório de ASP.NET Core](https://github.com/aspnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :

* [Transformação de declarações](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Autenticação de cookie](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Provedor de política personalizada-IAuthorizationPolicyProvider](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Opções e esquemas de autenticação dinâmica](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Declarações externas](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Seleção entre o cookie e outro esquema de autenticação com base na solicitação](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restringe o acesso a arquivos estáticos](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Executar os exemplos

* Selecione uma [ramificação](https://github.com/aspnet/AspNetCore). Por exemplo, `release/2.2`
* Clone ou baixe o [repositório ASP.NET Core](https://github.com/aspnet/AspNetCore).
* Verifique se você instalou a versão [SDK do .NET Core](https://www.microsoft.com/net/download/all) correspondente ao clone do repositório ASP.NET Core.
* Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo `dotnet run`com.

::: moniker-end
