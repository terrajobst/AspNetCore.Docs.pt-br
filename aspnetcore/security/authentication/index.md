---
title: Visão geral da autenticação ASP.NET Core
author: mjrousos
description: Saiba mais sobre a autenticação no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
uid: security/authentication/index
ms.openlocfilehash: 404904ecfa30d1fe7e47f0daaa423ddd6f1b06e8
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434324"
---
# <a name="overview-of-aspnet-core-authentication"></a>Visão geral da autenticação ASP.NET Core

Por [Mike Rousos](https://github.com/mjrousos)

A autenticação é o processo de determinação da identidade de um usuário. A [autorização](xref:security/authorization/introduction) é o processo de determinar se um usuário tem acesso a um recurso. No ASP.NET Core, a autenticação é tratada pela `IAuthenticationService`, que é usada pelo [middleware](xref:fundamentals/middleware/index)de autenticação. O serviço de autenticação usa manipuladores de autenticação registrados para concluir as ações relacionadas à autenticação. Exemplos de ações relacionadas à autenticação incluem:

* Autenticação de um usuário.
* Respondendo quando um usuário não autenticado tenta acessar um recurso restrito.

Os manipuladores de autenticação registrados e suas opções de configuração são chamados de "esquemas".

Os esquemas de autenticação são especificados pelo registro dos serviços de autenticação no `Startup.ConfigureServices`:

* Chamando um método de extensão específico de esquema após uma chamada para `services.AddAuthentication` (como `AddJwtBearer` ou `AddCookie`, por exemplo). Esses métodos de extensão usam [AuthenticationBuilder. addscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) para registrar esquemas com as configurações apropriadas.
* Com menos frequência, chamando [AuthenticationBuilder. addscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) diretamente.

Por exemplo, o código a seguir registra os serviços e manipuladores de autenticação para esquemas de autenticação de portador JWT e Cookie:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

O parâmetro `AddAuthentication` `JwtBearerDefaults.AuthenticationScheme` é o nome do esquema a ser usado por padrão quando um esquema específico não é solicitado.

Se vários esquemas forem usados, as políticas de autorização (ou atributos de autorização) poderão [especificar o esquema de autenticação (ou esquemas)](xref:security/authorization/limitingidentitybyscheme) que dependem para autenticar o usuário. No exemplo acima, o esquema de autenticação de cookie poderia ser usado especificando seu nome (`CookieAuthenticationDefaults.AuthenticationScheme` por padrão, embora um nome diferente possa ser fornecido ao chamar `AddCookie`).

Em alguns casos, a chamada para `AddAuthentication` é feita automaticamente por outros métodos de extensão. Por exemplo, ao usar a [identidade de ASP.NET Core](xref:security/authentication/identity), `AddAuthentication` é chamado internamente.

O middleware de autenticação é adicionado em `Startup.Configure` chamando o método de extensão <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> na `IApplicationBuilder`do aplicativo. Chamar `UseAuthentication` registra o middleware que usa os esquemas de autenticação registrados anteriormente. Chame `UseAuthentication` antes de qualquer middleware que dependa dos usuários que estão sendo autenticados. Ao usar o roteamento de ponto de extremidade, a chamada para `UseAuthentication` deve ir:

* Após `UseRouting`, para que as informações de rota estejam disponíveis para decisões de autenticação.
* Antes de `UseEndpoints`, para que os usuários sejam autenticados antes de acessar os pontos de extremidade.

## <a name="authentication-concepts"></a>Conceitos de autenticação

### <a name="authentication-scheme"></a>Esquema de autenticação

Um esquema de autenticação é um nome que corresponde a:

* Um manipulador de autenticação.
* Opções para configurar essa instância específica do manipulador.

Os esquemas são úteis como um mecanismo para se referir aos comportamentos de autenticação, desafio e proíba do manipulador associado. Por exemplo, uma política de autorização pode usar nomes de esquema para especificar qual esquema (ou esquemas) de autenticação deve ser usado para autenticar o usuário. Ao configurar a autenticação, é comum especificar o esquema de autenticação padrão. O esquema padrão é usado, a menos que um recurso solicite um esquema específico. Também é possível:

* Especifique esquemas padrão diferentes a serem usados para ações de autenticação, desafio e proibir.
* Combine vários esquemas em um usando [esquemas de política](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Manipulador de autenticação

Um manipulador de autenticação:

* É um tipo que implementa o comportamento de um esquema.
* É derivado de <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> ou <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.
* O tem a principal responsabilidade de autenticar usuários.

Com base na configuração do esquema de autenticação e no contexto de solicitação de entrada, manipuladores de autenticação:

* Construa <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objetos que representam a identidade do usuário se a autenticação for bem-sucedida.
* Retorne ' sem resultado ' ou ' falha ' se a autenticação não for bem-sucedida.
* Ter métodos de desafio e de proibir ações para quando os usuários tentarem acessar recursos:
  * Eles não são autorizados a acessar (proíba).
  * Quando eles não são autenticados (desafio).

### <a name="authenticate"></a>Authenticate

A ação de autenticação de um esquema de autenticação é responsável por construir a identidade do usuário com base no contexto da solicitação. Ele retorna um <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicando se a autenticação foi bem-sucedida e, em caso afirmativo, a identidade do usuário em um tíquete de autenticação. Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Os exemplos de autenticação incluem:

* Um esquema de autenticação de cookie que constrói a identidade do usuário a partir de cookies.
* Um esquema de portador JWT desserializando e Validando um token de portador JWT para construir a identidade do usuário.

### <a name="challenge"></a>Desafio

Um desafio de autenticação é invocado pela autorização quando um usuário não autenticado solicita um ponto de extremidade que requer autenticação. Um desafio de autenticação é emitido, por exemplo, quando um usuário anônimo solicita um recurso restrito ou clica em um link de logon. A autorização invoca um desafio usando os esquemas de autenticação especificados ou o padrão, se nenhum for especificado. Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Os exemplos de desafio de autenticação incluem:

* Um esquema de autenticação de cookie redirecionando o usuário para uma página de logon.
* Um esquema de portador JWT que retorna um resultado 401 com um cabeçalho `www-authenticate: bearer`.

Uma ação de desafio deve permitir que o usuário saiba qual mecanismo de autenticação usar para acessar o recurso solicitado.

### <a name="forbid"></a>Proíba

Uma ação proibir do esquema de autenticação é chamada por autorização quando um usuário autenticado tenta acessar um recurso que não tem permissão para acessar. Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. A autenticação proíbem exemplos incluem:
* Um esquema de autenticação de cookie redirecionando o usuário para uma página indicando que o acesso foi proibido.
* Um esquema de portador JWT que retorna um resultado 403.
* Um esquema de autenticação personalizado redirecionando para uma página na qual o usuário pode solicitar acesso ao recurso.

Uma ação proíba pode permitir que o usuário saiba:

* Eles são autenticados.
* Eles não têm permissão para acessar o recurso solicitado.

Consulte os seguintes links para obter as diferenças entre o desafio e o proíba:

* [Desafio e proíba com um manipulador de recursos operacionais](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).
* [Diferenças entre desafio e proibir](xref:security/authorization/secure-data#challenge).

## <a name="authentication-providers-per-tenant"></a>Provedores de autenticação por locatário

O ASP.NET Core Framework não tem uma solução interna para autenticação multilocatário.
Embora seja certamente possível para os clientes escreverem um, usando os recursos internos, recomendamos que os clientes examinem o [Orchard Core](https://www.orchardcore.net/) para essa finalidade.

O Orchard Core é:

* Uma estrutura de aplicativos modular e multilocatários de software livre criada com ASP.NET Core.
* Um CMS (sistema de gerenciamento de conteúdo) criado sobre essa estrutura de aplicativo.

Consulte a fonte do [Orchard Core](https://github.com/OrchardCMS/OrchardCore) para obter um exemplo de provedores de autenticação por locatário.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
