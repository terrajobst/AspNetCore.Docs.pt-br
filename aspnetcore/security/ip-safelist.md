---
title: IP do cliente da assafe para ASP.NET Core
author: damienbod
description: Saiba como escrever filtros de middleware ou de ação para validar endereços IP remotos em uma lista de endereços IP aprovados.
ms.author: riande
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca5b0f8088773027f7403120247cbeca8900bcf5
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034338"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>IP do cliente da assafe para ASP.NET Core

Por [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)
 
Este artigo mostra três maneiras de implementar uma lista de permissões de IP (também conhecida como whitelist) em um aplicativo ASP.NET Core. Você pode usar:

* Middleware para verificar o endereço IP remoto de cada solicitação.
* Filtros de ação para verificar o endereço IP remoto de solicitações para controladores ou métodos de ação específicos.
* Razor Pages filtros para verificar o endereço IP remoto de solicitações para páginas Razor.

Em cada caso, uma cadeia de caracteres contendo endereços IP de cliente aprovados é armazenada em uma configuração de aplicativo. O middleware ou o filtro analisa a cadeia de caracteres em uma lista e verifica se o IP remoto está na lista. Caso contrário, um código de status HTTP 403 proibido será retornado.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="the-safelist"></a>A SafeList

A lista é configurada no arquivo *appSettings. JSON* . É uma lista delimitada por ponto-e-vírgula e pode conter endereços IPv4 e IPv6.

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>Middleware

O método `Configure` adiciona o middleware e passa a cadeia de caracteres de forma segura para ele em um parâmetro de construtor.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

O middleware analisa a cadeia de caracteres em uma matriz e procura o endereço IP remoto na matriz. Se o endereço IP remoto não for encontrado, o middleware retornará HTTP 401 proibido. Esse processo de validação é ignorado para solicitações HTTP Get.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtro de ação

Se você quiser uma forma segura somente para controladores específicos ou métodos de ação, use um filtro de ação. Veja um exemplo: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckFilter.cs)]

O filtro de ação é adicionado ao contêiner de serviços.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

O filtro pode ser usado em um controlador ou método de ação.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

No aplicativo de exemplo, o filtro é aplicado ao método `Get`. Então, quando você testa o aplicativo enviando uma `Get` solicitação de API, o atributo está validando o endereço IP do cliente. Quando você testa chamando a API com qualquer outro método HTTP, o middleware está validando o IP do cliente.

## <a name="razor-pages-filter"></a>Filtro de Razor Pages 

Se você quiser uma assafe para um aplicativo Razor Pages, use um filtro Razor Pages. Veja um exemplo: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckPageFilter.cs)]

Esse filtro é habilitado adicionando-o à coleção de filtros MVC.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

Quando você executa o aplicativo e solicita uma página Razor, o filtro de Razor Pages está validando o IP do cliente.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o middleware ASP.NET Core](xref:fundamentals/middleware/index).
