---
title: ASP.NET Core plataformas suportadas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core mais incrivelmente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 042fbb1b2c7f92b7dc6443319f3f195a12a55adc
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963876"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core plataformas suportadas com suporte

Por [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Requisitos do navegador

### <a name="blazor-webassembly"></a>Webassembly mais incrivelmente

| Navegador                          | Versão               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Atual               |
| Mozilla Firefox                  | Atual               |
| Google Chrome, incluindo Android | Atual               |
| Safari, incluindo iOS            | Atual               |
| Microsoft Internet Explorer      | Sem suporte&dagger; |

O &dagger;Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).

### <a name="blazor-server"></a>Servidor mais incrivelmente

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
