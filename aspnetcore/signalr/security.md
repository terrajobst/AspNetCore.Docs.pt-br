---
title: Considerações de segurança no Signalr ASP.NET Core
author: bradygaster
description: Saiba como usar a autenticação e a autorização no Signalr ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: a52db2ff51c55f7299d63aa3c7398f99727e0694
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746554"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>Considerações de segurança no Signalr ASP.NET Core

Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)

Este artigo fornece informações sobre como proteger o Signalr.

## <a name="cross-origin-resource-sharing"></a>Compartilhamento de recursos entre origens

O [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/) pode ser usado para permitir conexões de sinalização entre origens no navegador. Se o código JavaScript estiver hospedado em um domínio diferente do aplicativo Signalr, o [middleware CORS](xref:security/cors) deverá ser habilitado para permitir que o JavaScript se conecte ao aplicativo signalr. Permitir solicitações entre origens somente de domínios nos quais você confia ou controla. Por exemplo:

* Seu site está hospedado em`http://www.example.com`
* Seu aplicativo Signalr está hospedado em`http://signalr.example.com`

O CORS deve ser configurado no aplicativo Signalr para permitir somente a origem `www.example.com`.

Para obter mais informações sobre como configurar o CORS, consulte [habilitar solicitações entre origens (CORS)](xref:security/cors). O signalr **requer** as seguintes políticas de CORS:

* Permitir as origens esperadas específicas. Permitir qualquer origem é possível, mas **não** é seguro ou recomendado.
* Os métodos `GET` http `POST` e devem ser permitidos.
* As credenciais devem ser habilitadas, mesmo quando a autenticação não é usada.

Por exemplo, a seguinte política de CORS permite que um cliente de navegador do `https://example.com` signalr hospedado no acesse o aplicativo `https://signalr.example.com`signalr hospedado em:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> O signalr não é compatível com o recurso CORS interno no serviço Azure App.

## <a name="websocket-origin-restriction"></a>Restrição de origem do WebSocket

::: moniker range=">= aspnetcore-2.2"

As proteções fornecidas pelo CORS não se aplicam ao WebSockets. Para a restrição de origem em WebSockets, leia a [restrição de origem de WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

As proteções fornecidas pelo CORS não se aplicam ao WebSockets. Navegadores **não**:

* Executam solicitações de simulação de CORS.
* Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.

No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket. Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.

No ASP.NET Core 2,1 e posterior, a validação de cabeçalho pode ser obtida usando um middleware personalizado **colocado `UseSignalR`antes e middleware de autenticação** no `Configure`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado. Esses cabeçalhos **não** devem ser usados como um mecanismo de autenticação.

::: moniker-end

## <a name="access-token-logging"></a>Log de token de acesso

Ao usar Websockets ou eventos enviados pelo servidor, o cliente de navegador envia o token de acesso na cadeia de caracteres de consulta. O recebimento do token de acesso por meio da cadeia de caracteres de consulta geralmente `Authorization` é tão seguro quanto usar o cabeçalho padrão. Você sempre deve usar HTTPS para garantir uma conexão segura de ponta a ponta entre o cliente e o servidor. Muitos servidores Web registram a URL para cada solicitação, incluindo a cadeia de caracteres de consulta. O registro em log das URLs pode registrar o token de acesso. O ASP.NET Core registra em log a URL para cada solicitação por padrão, o que incluirá a cadeia de caracteres de consulta. Por exemplo:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

Se você tiver dúvidas sobre como registrar esses dados nos logs do servidor, poderá desabilitar esse log totalmente Configurando `Microsoft.AspNetCore.Hosting` o agente para `Warning` o nível ou acima (essas mensagens são gravadas no `Info` nível). Consulte a documentação sobre [filtragem de log](xref:fundamentals/logging/index#log-filtering) para obter mais informações. Se você ainda quiser registrar determinadas informações de solicitação, poderá [escrever um middleware](xref:fundamentals/middleware/write) para registrar os dados necessários e filtrar o valor da cadeia de `access_token` caracteres de consulta (se houver).

## <a name="exceptions"></a>Exceções

As mensagens de exceção geralmente são consideradas dados confidenciais que não devem ser revelados a um cliente. Por padrão, o Signalr não envia os detalhes de uma exceção gerada por um método de Hub para o cliente. Em vez disso, o cliente recebe uma mensagem genérica indicando que ocorreu um erro. A entrega de mensagem de exceção ao cliente pode ser substituída (por exemplo, em desenvolvimento [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options)ou teste) com. As mensagens de exceção não devem ser expostas ao cliente em aplicativos de produção.

## <a name="buffer-management"></a>Gerenciamento de buffer

O signalr usa buffers por conexão para gerenciar mensagens de entrada e saída. Por padrão, o Signalr limita esses buffers a 32 KB. A maior mensagem que um cliente ou servidor pode enviar é 32 KB. A memória máxima consumida por uma conexão para mensagens é de 32 KB. Se suas mensagens forem sempre menores que 32 KB, você poderá reduzir o limite, que:

* Impede que um cliente possa enviar uma mensagem maior.
* O servidor nunca precisará alocar buffers grandes para aceitar mensagens.

Se suas mensagens forem maiores que 32 KB, você poderá aumentar o limite. Aumentar esse limite significa:

* O cliente pode fazer com que o servidor aloque buffers de memória grandes.
* A alocação de servidor de buffers grandes pode reduzir o número de conexões simultâneas.

Há limites para mensagens de entrada e saída, ambas podem ser configuradas no [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) objeto configurado em `MapHub`:

* `ApplicationMaxBufferSize`representa o número máximo de bytes do cliente que o servidor armazena em buffer. Se o cliente tentar enviar uma mensagem maior que esse limite, a conexão poderá ser fechada.
* `TransportMaxBufferSize`representa o número máximo de bytes que o servidor pode enviar. Se o servidor tentar enviar uma mensagem (incluindo valores de retorno dos métodos de Hub) maior que esse limite, uma exceção será lançada.

Definir o limite para `0` desabilitar o limite. A remoção do limite permite que um cliente envie uma mensagem de qualquer tamanho. Clientes mal-intencionados que enviam mensagens grandes podem causar o excesso de memória a ser alocada. O uso excessivo de memória pode reduzir significativamente o número de conexões simultâneas.
