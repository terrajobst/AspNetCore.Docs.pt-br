---
title: Introdução à autorização no ASP.NET Core
author: rick-anderson
description: Aprenda as noções básicas de autorização e como funciona a autorização em aplicativos ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: b5e60b3c256941fff5e54e1a02e077c34c535902
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693863"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="2655e-103">Introdução à autorização no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2655e-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="2655e-104">Autorização é o processo que determina o que um usuário pode fazer.</span><span class="sxs-lookup"><span data-stu-id="2655e-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="2655e-105">Por exemplo, um usuário administrativo tem permissão para criar uma biblioteca de documentos e adicionar, editar e excluir documentos.</span><span class="sxs-lookup"><span data-stu-id="2655e-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="2655e-106">Um usuário não administrativo trabalhando com esta biblioteca só está autorizado a ler os documentos.</span><span class="sxs-lookup"><span data-stu-id="2655e-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="2655e-107">A autorização é ortogonal e independente da autenticação.</span><span class="sxs-lookup"><span data-stu-id="2655e-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="2655e-108">No entanto, a autorização requer um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="2655e-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="2655e-109">A autenticação é o processo de garantir quem é um usuário.</span><span class="sxs-lookup"><span data-stu-id="2655e-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="2655e-110">A autenticação pode criar uma ou mais identidades para o usuário atual.</span><span class="sxs-lookup"><span data-stu-id="2655e-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="2655e-111">Para obter mais informações sobre autenticação no ASP.NET Core, consulte <xref:security/authentication/index>.</span><span class="sxs-lookup"><span data-stu-id="2655e-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="2655e-112">Tipos de autorização</span><span class="sxs-lookup"><span data-stu-id="2655e-112">Authorization types</span></span>

<span data-ttu-id="2655e-113">A autorização do ASP.NET Core fornece um modelo simples de [funções](xref:security/authorization/roles) declarativas [baseado em políticas](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="2655e-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="2655e-114">Ela é expressa em requisitos e os manipuladores avaliam as reivindicações de um usuário em relação aos requisitos.</span><span class="sxs-lookup"><span data-stu-id="2655e-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="2655e-115">Verificações imperativas podem ser baseadas em políticas simples ou políticas que avaliem a identidade do usuário e propriedades do recurso que o usuário está tentando acessar.</span><span class="sxs-lookup"><span data-stu-id="2655e-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="2655e-116">{1&gt;Namespaces&lt;1}</span><span class="sxs-lookup"><span data-stu-id="2655e-116">Namespaces</span></span>

<span data-ttu-id="2655e-117">Componentes de autorização, incluindo os atributo `AuthorizeAttribute` e `AllowAnonymousAttribute` , são encontrados no namespace `Microsoft.AspNetCore.Authorization`.</span><span class="sxs-lookup"><span data-stu-id="2655e-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="2655e-118">Consulte a documentação em [autorização simples](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="2655e-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
