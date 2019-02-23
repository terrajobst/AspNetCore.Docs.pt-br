---
title: Considerações de segurança no SignalR do ASP.NET Core
author: bradygaster
description: Saiba como usar a autenticação e autorização no SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: 6e9f849ed856cf1cbf989b8b16cab5209c465471
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56743781"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>Considerações de segurança no SignalR do ASP.NET Core

Por [Andrew Stanton-Nurse](https://twitter.com/anurse)

Este artigo fornece informações sobre como proteger o SignalR.

## <a name="cross-origin-resource-sharing"></a>Compartilhamento de recursos entre origens

[Recursos entre origens (CORS) compartilhamento](https://www.w3.org/TR/cors/) pode ser usado para permitir conexões do SignalR entre origens no navegador. Se o código JavaScript é hospedado em um domínio diferente do aplicativo do SignalR, [middleware CORS](xref:security/cors) deve estar habilitado para permitir que o JavaScript para se conectar ao aplicativo SignalR. Permitir solicitações entre origens de domínios que confiáveis ou controle. Por exemplo:

* O site é hospedado em `http://www.example.com`
* Seu aplicativo SignalR é hospedado em `http://signalr.example.com`

CORS devem ser configurado no aplicativo do SignalR para permitir somente a origem `www.example.com`.

Para obter mais informações sobre como configurar o CORS, consulte [habilitar solicitações de entre origens (CORS)](xref:security/cors). O SignalR **requer** as seguintes políticas CORS:

* Permitir que as origens esperadas específicas. Permitir qualquer origem é possível, mas está **não** segura ou recomendada.
* Métodos HTTP `GET` e `POST` devem ser permitidos.
* Credenciais devem ser habilitadas, mesmo quando a autenticação não é usada.

Por exemplo, a seguinte política CORS permite que um cliente de navegador do SignalR hospedado no `https://example.com` para acessar o aplicativo de SignalR hospedado em `https://signalr.example.com`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

> [!NOTE]
> O SignalR não é compatível com o recurso interno de CORS no serviço de aplicativo do Azure.

## <a name="websocket-origin-restriction"></a>WebSocket Origin Restriction

::: moniker range=">= aspnetcore-2.2"

As proteções fornecidas pelo CORS não se aplicam ao WebSockets. Para a restrição de origem sobre WebSockets, leia [restrição de origem de WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

As proteções fornecidas pelo CORS não se aplicam ao WebSockets. Navegadores **não**:

* Executam solicitações de simulação de CORS.
* Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.

No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket. Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.

No ASP.NET Core 2.1 e posterior, validação de cabeçalho pode ser obtida usando um middleware personalizado colocado **antes de `UseSignalR`e o middleware de autenticação** em `Configure`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado. Esses cabeçalhos devem **não** ser usado como um mecanismo de autenticação.

::: moniker-end

## <a name="access-token-logging"></a>Log de token de acesso

Ao usar WebSockets ou Server-Sent eventos, o cliente de navegador envia o token de acesso na cadeia de caracteres de consulta. Receber o token de acesso por meio da cadeia de caracteres de consulta é geralmente tão seguro quanto usar o padrão `Authorization` cabeçalho. Você sempre deve usar HTTPS para garantir uma conexão segura de ponta a ponta entre o cliente e o servidor. A URL para cada solicitação, incluindo a cadeia de caracteres de consulta de log de muitos servidores web. Registro em log as URLs pode registrar o token de acesso. ASP.NET Core registra a URL para cada solicitação, por padrão, o que incluirá a cadeia de caracteres de consulta. Por exemplo:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

Se você tiver dúvidas sobre o log de dados com os logs do servidor, você pode desabilitar esse log inteiramente, configurando o `Microsoft.AspNetCore.Hosting` agente para o `Warning` nível ou superior (essas mensagens são gravadas em `Info` nível). Consulte a documentação sobre [filtragem de Log](xref:fundamentals/logging/index#log-filtering) para obter mais informações. Se você ainda quiser registrar determinadas informações de solicitação, você poderá [escrever um middleware](xref:fundamentals/middleware/write) para registrar os dados necessários e filtrar o `access_token` valor de cadeia de caracteres de consulta (se presente).

## <a name="exceptions"></a>Exceções

Mensagens de exceção são geralmente consideradas dados confidenciais que não devem ser revelados para um cliente. Por padrão, o SignalR não envia os detalhes de uma exceção gerada por um método de hub para o cliente. Em vez disso, o cliente recebe uma mensagem genérica indicando que ocorreu um erro. Entrega de mensagem de exceção para o cliente pode ser substituída (por exemplo, no desenvolvimento ou teste) com [ `EnableDetailedErrors` ](xref:signalr/configuration#configure-server-options). Mensagens de exceção não devem ser expostas ao cliente em aplicativos de produção.

## <a name="buffer-management"></a>Gerenciamento de buffer

O SignalR usa buffers por conexão para gerenciar mensagens de entrada e saídas. Por padrão, o SignalR limita esses buffers para 32 KB. A mensagem maior que um cliente ou servidor pode enviar é 32 KB. O máximo de memória consumido por uma conexão de mensagens é 32 KB. Se as mensagens são sempre menores que 32 KB, você pode reduzir o limite, que:

* Impede que um cliente que está sendo capaz de enviar uma mensagem maior.
* O servidor nunca será necessário alocar buffers grandes para aceitar mensagens.

Se suas mensagens forem maiores que 32 KB, você pode aumentar o limite. Aumentar esse limite significa:

* O cliente pode fazer com que o servidor para alocar buffers de memória grandes.
* Alocação de servidor de buffers grandes pode reduzir o número de conexões simultâneas.

Há limites para mensagens de entrada e saídas, ambos podem ser configuradas sobre o [ `HttpConnectionDispatcherOptions` ](xref:signalr/configuration#configure-server-options) objeto configurado no `MapHub`:

* `ApplicationMaxBufferSize` representa o número máximo de bytes do cliente que os buffers de servidor. Se o cliente tenta enviar uma mensagem maior que esse limite, a conexão poderá ser fechada.
* `TransportMaxBufferSize` representa o número máximo de bytes que o servidor pode enviar. Se o servidor tenta enviar uma mensagem (incluindo valores de retorno de métodos de hub) maiores que esse limite, uma exceção será lançada.

Definir o limite como `0` desabilita o limite. Remover o limite permite que um cliente enviar uma mensagem de qualquer tamanho. Os clientes mal-intencionados enviando mensagens extensas podem provocar alocação de memória em excesso. Uso de memória em excesso pode reduzir significativamente o número de conexões simultâneas.
