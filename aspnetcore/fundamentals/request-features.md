---
title: Solicitar recursos no ASP.NET Core
author: ardalis
description: Saiba mais sobre detalhes de implementação de servidor Web relacionados a solicitações HTTP e as respostas que são definidas em interfaces para o ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659673"
---
# <a name="request-features-in-aspnet-core"></a>Solicitar recursos no ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

Os detalhes de implementação de servidor Web relacionados a solicitações HTTP e respostas são definidos em interfaces. Essas interfaces são usadas pelas implementações de servidor e pelo middleware para criar e modificar o pipeline de hospedagem do aplicativo.

## <a name="feature-interfaces"></a>Interfaces de recurso

O ASP.NET Core define várias interfaces de recurso HTTP em `Microsoft.AspNetCore.Http.Features`, que são usadas pelos servidores para identificar os recursos para os quais eles dão suporte. As seguintes interfaces de recurso manipulam solicitações e respostas de retorno:

`IHttpRequestFeature` Define a estrutura de uma solicitação HTTP, incluindo o protocolo, o caminho, a cadeia de caracteres de consulta, os cabeçalhos e o corpo.

`IHttpResponseFeature` Define a estrutura de uma resposta HTTP, incluindo o código de status, os cabeçalhos e o corpo da resposta.

`IHttpAuthenticationFeature` Define o suporte para identificar os usuários com base em um `ClaimsPrincipal` e especificando um manipulador de autenticação.

`IHttpUpgradeFeature` Define o suporte para [Upgrades de HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), que permitem ao cliente especificar quais protocolos adicionais ele desejará usar se o servidor quiser mudar os protocolos.

`IHttpBufferingFeature` Define métodos para desabilitar o buffer de solicitações e/ou respostas.

`IHttpConnectionFeature` Define propriedades para portas e endereços locais e remotos.

`IHttpRequestLifetimeFeature` Define o suporte para anular conexões ou detectar se uma solicitação foi encerrada prematuramente, como por uma desconexão do cliente.

`IHttpSendFileFeature` Define um método para enviar arquivos de forma assíncrona.

`IHttpWebSocketFeature` Define uma API para dar suporte a soquetes da Web.

`IHttpRequestIdentifierFeature` Adiciona uma propriedade que pode ser implementada para identificar as solicitações de forma exclusiva.

`ISessionFeature` Define abstrações `ISessionFactory` e `ISession` para dar suporte a sessões de usuário.

`ITlsConnectionFeature` Define uma API para recuperar os certificados de cliente.

`ITlsTokenBindingFeature` Define métodos para trabalhar com parâmetros de associação de token de TLS.

> [!NOTE]
> `ISessionFeature` não é um recurso de servidor, mas é implementado por `SessionMiddleware` (consulte [Gerenciando o estado do aplicativo](app-state.md)).

## <a name="feature-collections"></a>Coleções de recursos

A propriedade `Features` de `HttpContext` fornece uma interface para obter e definir os recursos HTTP disponíveis para a solicitação atual. Como a coleção de recursos é mutável, mesmo no contexto de uma solicitação, o middleware pode ser usado para modificar a coleção e adicionar suporte para recursos adicionais.

## <a name="middleware-and-request-features"></a>Middleware e recursos de solicitação

Embora os servidores sejam responsáveis por criar a coleção de recursos, o middleware pode adicionar recursos a essa coleção e consumi-los. Por exemplo, o `StaticFileMiddleware` acessa o recurso `IHttpSendFileFeature`. Se o recurso existir, ele será usado para enviar o arquivo estático solicitado de seu caminho físico. Caso contrário, um método alternativo mais lento será usado para enviar o arquivo. Quando disponível, o `IHttpSendFileFeature` permite que o sistema operacional abra o arquivo e faça uma cópia direta de modo kernel para a placa de rede.

Além disso, o middleware pode adicionar recursos à coleção de recursos estabelecida pelo servidor. Os recursos existentes podem até mesmo ser substituídos pelo middleware, permitindo que o middleware aumente a funcionalidade do servidor. Os recursos adicionados à coleção ficam disponíveis imediatamente para outro middleware ou para o próprio aplicativo subjacente posteriormente no pipeline de solicitação.

Combinando implementações personalizadas de servidor e melhorias específicas de middleware, o conjunto preciso de recursos exigido por um aplicativo pode ser construído. Isso permite que os recursos ausentes sejam adicionados, sem a necessidade de uma alteração no servidor, além de garantir que somente a quantidade mínima de recursos seja exposta, limitando a área da superfície de ataque e melhorando o desempenho.

## <a name="summary"></a>Resumo

As interfaces de recurso definem recursos HTTP específicos para os quais uma solicitação específica pode dar suporte. Os servidores definem coleções de recursos e o conjunto inicial de recursos compatíveis com o servidor, mas o middleware pode ser usado para aprimorar esses recursos.

## <a name="additional-resources"></a>Recursos adicionais

* [Servidores](xref:fundamentals/servers/index)
* [Middleware](xref:fundamentals/middleware/index)
* [OWIN (Open Web Interface para .NET)](xref:fundamentals/owin)
