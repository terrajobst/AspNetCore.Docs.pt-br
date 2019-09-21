---
title: ASP.NET Core plataformas suportadas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core mais incrivelmente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 8730417f772c84ebcccc449a5826126aa5c64abb
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168164"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core plataformas suportadas com suporte

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Requisitos do navegador

### <a name="blazor-webassembly"></a>WebAssembly Blazor

| Navegador                          | Versão               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Atual               |
| Mozilla Firefox                  | Atual               |
| Google Chrome, incluindo Android | Atual               |
| Safari, incluindo iOS            | Atual               |
| Microsoft Internet Explorer      | Sem suporte&dagger; |

O &dagger;Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).

### <a name="blazor-server"></a>Servidor Blazor

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
