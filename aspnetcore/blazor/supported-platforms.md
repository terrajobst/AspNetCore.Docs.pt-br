---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para Blazorde ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160126"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a>ASP.NET Core Blazor plataformas com suporte

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Requisitos de navegador

### <a name="opno-locblazor-webassembly"></a>Blazor Webassembly

| Navegador                          | Versão do               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Atual               |
| Mozilla Firefox                  | Atual               |
| Google Chrome, incluindo Android | Atual               |
| Safari, incluindo iOS            | Atual               |
| Microsoft Internet Explorer      | Sem suporte&dagger; |

O &dagger;Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).

### <a name="opno-locblazor-server"></a>Servidor de Blazor

| Navegador                          | Versão do    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Atual    |
| Mozilla Firefox                  | Atual    |
| Google Chrome, incluindo Android | Atual    |
| Safari, incluindo iOS            | Atual    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;outros suportes retroativos são necessários (por exemplo, as promessas podem ser adicionadas por meio de um pacote [polyfill.Io](https://polyfill.io/v3/) ).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/hosting-models>
