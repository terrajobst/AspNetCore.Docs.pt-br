---
title: Autorização baseada em recursos no ASP.NET Core
author: scottaddie
description: Saiba como implementar a autorização baseada em recursos em um aplicativo ASP.NET Core quando um atributo de autorização não for suficiente.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: acc931da1be0940fac72b0aabe07ab17ca7e63bd
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659998"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>Autorização baseada em recursos no ASP.NET Core

A estratégia de autorização depende do recurso que está sendo acessado. Considere um documento que tenha uma propriedade de autor. Somente o autor tem permissão para atualizar o documento. Consequentemente, o documento deve ser recuperado do armazenamento de dados antes que a avaliação de autorização possa ocorrer.

A avaliação de atributo ocorre antes da vinculação de dados e antes da execução do manipulador de página ou da ação que carrega o documento. Por esses motivos, a autorização declarativa com um atributo `[Authorize]` não é suficiente. Em vez disso, você pode invocar um método de autorização personalizado &mdash;a estilo conhecido como *autorização imperativa*.

::: moniker range=">= aspnetcore-3.0"
[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([como baixar](xref:index#how-to-download-a-sample)).
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([como baixar](xref:index#how-to-download-a-sample)).
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([como baixar](xref:index#how-to-download-a-sample)).
::: moniker-end

[Criar um aplicativo ASP.NET Core com os dados do usuário protegidos por autorização](xref:security/authorization/secure-data) contém um aplicativo de exemplo que usa a autorização baseada em recursos.

## <a name="use-imperative-authorization"></a>Usar autorização imperativa

A autorização é implementada como um serviço [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) e é registrada na coleção de serviços dentro da classe `Startup`. O serviço é disponibilizado por meio de [injeção de dependência](xref:fundamentals/dependency-injection) para manipuladores de página ou ações.

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` tem duas sobrecargas de método `AuthorizeAsync`: uma aceita o recurso e o nome da política e a outra aceita o recurso e uma lista de requisitos a serem avaliados.

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

No exemplo a seguir, o recurso a ser protegido é carregado em um objeto de `Document` personalizado. Uma sobrecarga de `AuthorizeAsync` é invocada para determinar se o usuário atual tem permissão para editar o documento fornecido. Uma política de autorização personalizada "EditPolicy" é fatorada na decisão. Consulte [autorização personalizada baseada em políticas](xref:security/authorization/policies) para obter mais informações sobre a criação de políticas de autorização.

> [!NOTE]
> Os exemplos de código a seguir pressupõem que a autenticação foi executada e definiu a propriedade `User`.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>Gravar um manipulador baseado em recursos

Escrever um manipulador para autorização baseada em recursos não é muito diferente de [escrever um manipulador de requisitos simples](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Crie uma classe de requisito personalizada e implemente uma classe de manipulador de requisitos. Para obter mais informações sobre como criar uma classe de requisito, consulte [requirements](xref:security/authorization/policies#requirements).

A classe Handler especifica o requisito e o tipo de recurso. Por exemplo, um manipulador que utiliza um `SameAuthorRequirement` e um recurso de `Document` segue:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

No exemplo anterior, imagine que `SameAuthorRequirement` é um caso especial de uma classe `SpecificAuthorRequirement` mais genérica. A classe `SpecificAuthorRequirement` (não mostrada) contém uma propriedade `Name` que representa o nome do autor. A propriedade `Name` pode ser definida como o usuário atual.

Registre o requisito e o manipulador no `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a>Requisitos operacionais

Se você estiver tomando decisões com base nos resultados de operações CRUD (criar, ler, atualizar, excluir), use a classe auxiliar [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) . Essa classe permite que você grave um único manipulador em vez de uma classe individual para cada tipo de operação. Para usá-lo, forneça alguns nomes de operação:

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

O manipulador é implementado da seguinte maneira, usando um requisito de `OperationAuthorizationRequirement` e um recurso de `Document`:

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

O manipulador anterior valida a operação usando o recurso, a identidade do usuário e a propriedade de `Name` do requisito.

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a>Desafio e proíba com um manipulador de recursos operacionais

Esta seção mostra como os resultados de desafio e proíba são processados e como o desafio e proíba são diferentes.

Para chamar um manipulador de recursos operacionais, especifique a operação ao invocar `AuthorizeAsync` em seu manipulador de página ou ação. O exemplo a seguir determina se o usuário autenticado tem permissão para exibir o documento fornecido.

> [!NOTE]
> Os exemplos de código a seguir pressupõem que a autenticação foi executada e definiu a propriedade `User`.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Se a autorização for realizada com sucesso, a página para exibir o documento será retornada. Se a autorização falhar, mas o usuário for autenticado, o retorno `ForbidResult` informará a qualquer middleware de autenticação que a autorização falhou. Um `ChallengeResult` é retornado quando A autenticação deve ser executada. Para clientes de navegador interativos, pode ser apropriado redirecionar o usuário para uma página de logon.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Se a autorização for realizada com sucesso, a exibição do documento será retornada. Se a autorização falhar, o retorno `ChallengeResult` informará a qualquer middleware de autenticação que a autorização falhou e o middleware poderá tomar a resposta apropriada. Uma resposta apropriada pode retornar um código de status 401 ou 403. Para clientes de navegador interativos, isso pode significar o redirecionamento do usuário para uma página de logon.

::: moniker-end
