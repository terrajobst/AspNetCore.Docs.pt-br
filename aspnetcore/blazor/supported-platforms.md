---
title: ASP.NET Core plataformas suportadas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core mais incrivelmente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/supported-platforms
ms.openlocfilehash: b769ee175cde7c9a613d7fb70949de129ca428d3
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211574"
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
