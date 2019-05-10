---
title: Migrar do Logging 2.1 para 2.2 ou 3.0
author: pakrym
description: Saiba como migrar um aplicativo ASP.NET Core que usa Logging do 2.1, 2.2 ou 3.0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892453"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migrar do Logging 2.1 para 2.2 ou 3.0

Este artigo descreve as etapas comuns para a migração de um aplicativo ASP.NET Core que usa `Microsoft.Extensions.Logging` do 2.1, 2.2 ou 3.0.

## <a name="21-to-22"></a>2.1 a 2.2

Criar manualmente `ServiceCollection` e chamar `AddLogging`.

exemplo 2.1:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

exemplo de 2.2:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2.1 a 3.0

No 3.0, use `LoggingFactory.Create`.

exemplo 2.1:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

exemplo 3.0:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Recursos adicionais

<xref:fundamentals/logging/index>