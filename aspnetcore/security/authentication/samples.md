---
title: Amostras de autenticação para o ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório do ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: 7b3c911d60ad4737ebd12ce6f7628ad624b11658
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897153"
---
# <a name="authentication-samples-for-aspnet-core"></a>Amostras de autenticação para o ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O [repositório do ASP.NET Core](https://github.com/aspnet/AspNetCore) contém os seguintes exemplos de autenticação na *AspNetCore/src/Security/samples* pasta:

* [Transformação de declarações](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Autenticação de cookie](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Provedor de política personalizada - IAuthorizationPolicyProvider](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Opções e esquemas de autenticação dinâmico](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Declarações externas](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Selecionando entre o cookie e o outro esquema de autenticação com base na solicitação](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restringe o acesso a arquivos estáticos](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Executar os exemplos

* Selecione uma [ramificação](https://github.com/aspnet/AspNetCore). Por exemplo, `release/2.2`
* Clone ou baixe o [repositório do ASP.NET Core](https://github.com/aspnet/AspNetCore).
* Verifique se você instalou o [SDK do .NET Core](https://www.microsoft.com/net/download/all) versão que corresponde o clone do repositório do ASP.NET Core.
* Navegue até um exemplo na *AspNetCore/src/Security/samples* e executar o exemplo com `dotnet run`.
