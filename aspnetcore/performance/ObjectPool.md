---
title: Reutilização de objeto com objectpool no ASP.NET Core
author: rick-anderson
description: Dicas para aumentar o desempenho em aplicativos ASP.NET Core usando o objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666106"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Reutilização de objeto com objectpool no ASP.NET Core

Por [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)e [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool> faz parte da infraestrutura de ASP.NET Core que dá suporte à manutenção de um grupo de objetos na memória para reutilização em vez de permitir que os objetos sejam coletados como lixo.

Talvez você queira usar o pool de objetos se os objetos que estão sendo gerenciados forem:

- Caro de alocar/inicializar.
- Representam alguns recursos limitados.
- Usado de forma previsível e frequente.

Por exemplo, o ASP.NET Core Framework usa o pool de objetos em alguns lugares para reutilizar <xref:System.Text.StringBuilder> instâncias. `StringBuilder` aloca e gerencia seus próprios buffers para armazenar dados de caractere. O ASP.NET Core usa regularmente `StringBuilder` para implementar recursos e reusá-los proporciona um benefício de desempenho.

O pool de objetos nem sempre melhora o desempenho:

- A menos que o custo de inicialização de um objeto seja alto, é geralmente mais lento obter o objeto do pool.
- Os objetos gerenciados pelo pool não são desalocados até que o pool seja desalocado.

Use o pool de objetos somente depois de coletar dados de desempenho usando cenários realistas para seu aplicativo ou biblioteca.

**Aviso: o `ObjectPool` não implementa `IDisposable`. Não é recomendável usá-lo com tipos que precisam de descarte.**

**Observação: o objectpool não coloca um limite no número de objetos que ele alocará, ele coloca um limite no número de objetos que será mantido.**

## <a name="concepts"></a>Conceitos

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-a abstração básica do pool de objetos. Usado para obter e retornar objetos.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-implemente isso para personalizar como um objeto é criado e como ele é *redefinido* quando retornado ao pool. Isso pode ser passado para um pool de objetos que você constrói diretamente... OR

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> atua como um alocador para a criação de pools de objetos.
<!-- REview, there is no ObjectPoolProvider<T> -->

O objectpool pode ser usado em um aplicativo de várias maneiras:

* Instanciando um pool.
* Registrando um pool na [injeção de dependência](xref:fundamentals/dependency-injection) (di) como uma instância.
* Registrar o `ObjectPoolProvider<>` em DI e usá-lo como uma fábrica.

## <a name="how-to-use-objectpool"></a>Como usar o objectpool

Chame <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> para obter um objeto e <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> para retornar o objeto.  Não há nenhum requisito para que você retorne todos os objetos. Se você não retornar um objeto, ele será coletado como lixo.

## <a name="objectpool-sample"></a>Exemplo de objectpool

O seguinte código:

* Adiciona `ObjectPoolProvider` ao contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) (di).
* Adiciona e configura `ObjectPool<StringBuilder>` para o contêiner DI.
* Adiciona o `BirthdayMiddleware`.

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

O código a seguir implementa `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
