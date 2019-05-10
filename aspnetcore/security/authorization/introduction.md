---
title: Introdução à autorização no ASP.NET Core
author: rick-anderson
description: Aprenda as Noções básicas de autorização e como funciona a autorização em aplicativos ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: 5465eb7875ebecd77b628376ef886db0ddd05025
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896973"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="0d40c-103">Introdução à autorização no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d40c-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="0d40c-104">Autorização é o processo que determina o que um usuário pode fazer.</span><span class="sxs-lookup"><span data-stu-id="0d40c-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="0d40c-105">Por exemplo, um usuário administrativo tem permissão para criar uma biblioteca de documentos e adicionar, editar e excluir documentos.</span><span class="sxs-lookup"><span data-stu-id="0d40c-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="0d40c-106">Um usuário não administrativo trabalhando com esta biblioteca só está autorizado a ler os documentos.</span><span class="sxs-lookup"><span data-stu-id="0d40c-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="0d40c-107">A autorização é ortogonal e independente de autenticação.</span><span class="sxs-lookup"><span data-stu-id="0d40c-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="0d40c-108">No entanto, a autorização requer um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="0d40c-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="0d40c-109">Autenticação é o processo de verificação de quem é um usuário.</span><span class="sxs-lookup"><span data-stu-id="0d40c-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="0d40c-110">A autenticação pode criar uma ou mais identidades para o usuário atual.</span><span class="sxs-lookup"><span data-stu-id="0d40c-110">Authentication may create one or more identities for the current user.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="0d40c-111">Tipos de autorização</span><span class="sxs-lookup"><span data-stu-id="0d40c-111">Authorization types</span></span>

<span data-ttu-id="0d40c-112">A autorização do ASP.NET Core fornece um modelo simples de [funções](xref:security/authorization/roles) declarativas [baseado em políticas](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="0d40c-112">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="0d40c-113">Ela é expressa em requisitos e os manipuladores avaliam as reivindicações de um usuário em relação aos requisitos.</span><span class="sxs-lookup"><span data-stu-id="0d40c-113">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="0d40c-114">Verificações imperativas podem ser baseadas em políticas simples ou políticas que avaliem a identidade do usuário e propriedades do recurso que o usuário está tentando acessar.</span><span class="sxs-lookup"><span data-stu-id="0d40c-114">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="0d40c-115">Namespaces</span><span class="sxs-lookup"><span data-stu-id="0d40c-115">Namespaces</span></span>

<span data-ttu-id="0d40c-116">Componentes de autorização, incluindo os atributo `AuthorizeAttribute` e `AllowAnonymousAttribute` , são encontrados no namespace `Microsoft.AspNetCore.Authorization`.</span><span class="sxs-lookup"><span data-stu-id="0d40c-116">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="0d40c-117">Consulte a documentação em [autorização simples](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="0d40c-117">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
