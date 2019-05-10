---
title: Visão geral sobre a segurança do ASP.NET Core
author: tdykstra
description: Saiba mais sobre conceitos básicos de autenticação, autorização e segurança no ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: security/index
ms.openlocfilehash: 933501411169d89c4b24edda743c47591aa7a87a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64881881"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="75659-103">Visão geral sobre a segurança do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75659-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="75659-104">O ASP.NET Core permite que desenvolvedores configurem e gerenciem facilmente a segurança de seus aplicativos.</span><span class="sxs-lookup"><span data-stu-id="75659-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="75659-105">O ASP.NET Core contém recursos para gerenciamento de autenticação, autorização, proteção de dados, imposição de HTTPS, segredos de aplicativo, proteção contra falsificação de solicitação e gerenciamento de CORS.</span><span class="sxs-lookup"><span data-stu-id="75659-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="75659-106">Esses recursos de segurança permitem que você crie aplicativos de ASP.NET Core robustos e seguros ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="75659-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="75659-107">Recursos de segurança do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75659-107">ASP.NET Core security features</span></span>

<span data-ttu-id="75659-108">O ASP.NET Core fornece várias ferramentas e bibliotecas para proteger seus aplicativos, incluindo provedores de identidade internos, mas você pode usar serviços de identidade de terceiros, como Facebook, Twitter e LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="75659-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in Identity providers but you can use 3rd party identity services such as Facebook, Twitter, or LinkedIn.</span></span> <span data-ttu-id="75659-109">Com o ASP.NET Core, você pode gerenciar facilmente os segredos do aplicativo, que são uma maneira de armazenar e usar informações confidenciais sem a necessidade de expô-los no código.</span><span class="sxs-lookup"><span data-stu-id="75659-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="75659-110">Autenticação versus Autorização</span><span class="sxs-lookup"><span data-stu-id="75659-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="75659-111">A autenticação é um processo em que um usuário fornece credenciais que são comparadas àquelas armazenadas em um sistema operacional, num banco de dados, no aplicativo ou no recurso.</span><span class="sxs-lookup"><span data-stu-id="75659-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="75659-112">Se elas corresponderem, os usuários se autenticarão com êxito e, assim, poderão realizar ações para as quais são autorizados, durante um processo de autorização.</span><span class="sxs-lookup"><span data-stu-id="75659-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="75659-113">A autorização é o processo que determina o que um usuário pode fazer.</span><span class="sxs-lookup"><span data-stu-id="75659-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="75659-114">Outra forma de pensar na autenticação é considerá-la como uma maneira de entrar em um espaço, como um servidor, um banco de dados, um aplicativo ou um recurso, ao passo que a autorização refere-se a quais ações o usuário poderá executar em que objetos dentro desse espaço (servidor, banco de dados ou aplicativo).</span><span class="sxs-lookup"><span data-stu-id="75659-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="75659-115">Vulnerabilidades comuns no software</span><span class="sxs-lookup"><span data-stu-id="75659-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="75659-116">O ASP.NET Core e o EF contêm recursos que ajudam a proteger seus aplicativos e impedir violações de segurança.</span><span class="sxs-lookup"><span data-stu-id="75659-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="75659-117">A seguinte lista de links leva à documentação com detalhe de técnicas para evitar as vulnerabilidades de segurança mais comuns em aplicativos Web:</span><span class="sxs-lookup"><span data-stu-id="75659-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="75659-118">Ataques de script entre sites</span><span class="sxs-lookup"><span data-stu-id="75659-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="75659-119">Ataques de injeção de SQL</span><span class="sxs-lookup"><span data-stu-id="75659-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="75659-120">CSRF (solicitação intersite forjada)</span><span class="sxs-lookup"><span data-stu-id="75659-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="75659-121">Ataques de redirecionamento aberto</span><span class="sxs-lookup"><span data-stu-id="75659-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="75659-122">Há mais vulnerabilidades sobre as quais você deve estar atento.</span><span class="sxs-lookup"><span data-stu-id="75659-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="75659-123">Para obter mais informações, veja os outros artigos na seção **Segurança e identidade** do sumário.</span><span class="sxs-lookup"><span data-stu-id="75659-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
