---
title: ASP.NET Core plataformas suportadas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core mais incrivelmente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 01f3a55a8536feedf713e07ea3724a0bc51e7c63
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "68948246"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core plataformas suportadas com suporte

Por [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Requisitos do navegador

### <a name="blazor-client-side"></a>Blazor no lado do cliente

| Navegador                          | Versão               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Atual               |
| Mozilla Firefox                  | Atual               |
| Google Chrome, incluindo Android | Atual               |
| Safari, incluindo iOS            | Atual               |
| Microsoft Internet Explorer      | Sem suporte&dagger; |

O &dagger;Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).

### <a name="blazor-server-side"></a>Blazor no lado do servidor

| Navegador                          | Versão    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Atual    |
| Mozilla Firefox                  | Atual    |
| Google Chrome, incluindo Android | Atual    |
| Safari, incluindo iOS            | Atual    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Os suportes retroativos adicionais são necessários (por exemplo, as promessas podem ser adicionadas por meio de um pacote [polyfill.Io](https://polyfill.io/v3/) ).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/hosting-models>
