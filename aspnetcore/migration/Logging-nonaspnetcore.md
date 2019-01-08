---
title: Migrar do Logging 2.1 para 2.2 ou 3.0
author: pakrym
description: Saiba como migrar um aplicativo ASP.NET Core que usa Logging do 2.1, 2.2 ou 3.0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099466"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="a045d-103">Migrar do Logging 2.1 para 2.2 ou 3.0</span><span class="sxs-lookup"><span data-stu-id="a045d-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="a045d-104">Este artigo descreve as etapas comuns para a migração de um aplicativo ASP.NET Core que usa `Microsoft.Extensions.Logging` do 2.1, 2.2 ou 3.0.</span><span class="sxs-lookup"><span data-stu-id="a045d-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="a045d-105">2.1 para 2.2</span><span class="sxs-lookup"><span data-stu-id="a045d-105">2.1 to 2.2</span></span>

<span data-ttu-id="a045d-106">Criar manualmente `ServiceCollection` e chamar `AddLogging`.</span><span class="sxs-lookup"><span data-stu-id="a045d-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="a045d-107">exemplo 2.1:</span><span class="sxs-lookup"><span data-stu-id="a045d-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="a045d-108">exemplo de 2.2:</span><span class="sxs-lookup"><span data-stu-id="a045d-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="a045d-109">2.1 a 3.0</span><span class="sxs-lookup"><span data-stu-id="a045d-109">2.1 to 3.0</span></span>

<span data-ttu-id="a045d-110">No 3.0, use `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="a045d-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="a045d-111">exemplo 2.1:</span><span class="sxs-lookup"><span data-stu-id="a045d-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="a045d-112">exemplo 3.0:</span><span class="sxs-lookup"><span data-stu-id="a045d-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="a045d-113">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a045d-113">Additional resources</span></span>

<xref:fundamentals/logging/index>