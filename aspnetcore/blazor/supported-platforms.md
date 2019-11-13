---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para Blazorde ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/supported-platforms
ms.openlocfilehash: de51296cc8785474e1c1406cfd5d4e5bd4050172
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962740"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a>ASP.NET Core Blazor plataformas com suporte

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Requisitos do navegador

### <a name="opno-locblazor-webassembly"></a>Blazor Webassembly

| Navegador                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Atual               |
| Mozilla Firefox                  | Atual               |
| Google Chrome, incluindo Android | Atual               |
| Safari, incluindo iOS            | Atual               |
| Microsoft Internet Explorer      | Sem suporte&dagger; |

&dagger;Microsoft o Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).

### <a name="opno-locblazor-server"></a>Servidor de Blazor

| Navegador                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Atual    |
| Mozilla Firefox                  | Atual    |
| Google Chrome, incluindo Android | Atual    |
| Safari, incluindo iOS            | Atual    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;outros suportes retroativos são necessários (por exemplo, as promessas podem ser adicionadas por meio de um pacote [polyfill.Io](https://polyfill.io/v3/) ).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/hosting-models>
