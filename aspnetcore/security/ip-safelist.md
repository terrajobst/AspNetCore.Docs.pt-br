---
title: Lista segura IP do cliente para o ASP.NET Core
author: damienbod
description: Saiba como filtros de ação ou de Middleware para validar os endereços IP remotos em relação a uma lista de endereços IP aprovados de gravação.
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: cfbb50ea33ae3af577f13b00bccc75fe0be57f79
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898143"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Lista segura IP do cliente para o ASP.NET Core

Por [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)
 
Este artigo mostra três maneiras de implementar uma lista segura IP (também conhecido como uma lista de permissões) em um aplicativo ASP.NET Core. Você pode usar:

* Middleware para verificar o endereço IP remoto de cada solicitação.
* Filtros de ação para verificar o endereço IP remoto de solicitações para controladores específicos ou métodos de ação.
* Filtros de páginas do Razor para verificar o endereço IP remoto de solicitações de páginas do Razor.

O aplicativo de exemplo ilustra as duas abordagens. Em cada caso, uma cadeia de caracteres que contém os endereços IP de cliente aprovados é armazenada em uma configuração de aplicativo. O middleware ou filtro analisa a cadeia de caracteres em uma lista e verifica se o IP remoto estiver na lista. Caso contrário, será retornado um código de status HTTP 403 Proibido.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="the-safelist"></a>A lista segura

A lista é configurada na *appSettings. JSON* arquivo. Ele é uma lista delimitada por ponto e vírgula e pode conter endereços IPv4 e IPv6.

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>Middleware

O `Configure` método adiciona o middleware e passa a cadeia de caracteres de lista segura para ele em um parâmetro de construtor.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=7)]

O middleware analisa a cadeia de caracteres em uma matriz e procura o endereço IP remoto na matriz. Se o endereço IP remoto não for encontrado, o middleware retornará HTTP 401 proibido. Esse processo de validação é ignorado para solicitações HTTP Get.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtro de ação

Se você quiser uma lista segura somente para controladores específicos ou métodos de ação, use um filtro de ação. Veja um exemplo: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

O filtro de ação é adicionado ao contêiner de serviços.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

O filtro, em seguida, pode ser usado em um método de ação ou controlador.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

No aplicativo de exemplo, o filtro é aplicado para o `Get` método. Portanto, quando você testar o aplicativo, enviando um `Get` solicitação de API, o atributo é validar o endereço IP do cliente. Quando você testa chamando a API com qualquer outro método HTTP, o middleware é validar o IP do cliente.

## <a name="razor-pages-filter"></a>Filtro de páginas do Razor 

Se você quiser uma lista segura para um aplicativo páginas Razor, use um filtro de páginas do Razor. Veja um exemplo: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

Esse filtro é habilitado adicionando-o à coleção de filtros MVC.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

Quando você executa o aplicativo e solicita uma página Razor, o filtro de páginas do Razor é validar o IP do cliente.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o Middleware do ASP.NET Core](xref:fundamentals/middleware/index).
