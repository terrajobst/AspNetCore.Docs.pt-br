---
title: Visão geral das APIs de consumidor para o ASP.NET Core
author: rick-anderson
description: Receba uma breve visão geral do consumidor várias APIs disponíveis dentro da biblioteca de proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: b0d11d097ee2d448b6781f6fa84445f6400fbc76
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087547"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Visão geral das APIs de consumidor para o ASP.NET Core

O `IDataProtectionProvider` e `IDataProtector` interfaces são as interfaces básicas por meio dos quais os consumidores usam o sistema de proteção de dados. Eles estão localizados na [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) pacote.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

A interface de provedor representa a raiz do sistema de proteção de dados. Diretamente, ele não pode ser usado para proteger ou desproteger dados. Em vez disso, o consumidor deve obter uma referência a um `IDataProtector` chamando `IDataProtectionProvider.CreateProtector(purpose)`, onde o objetivo é uma cadeia de caracteres que descreve o caso de uso pretendido do consumidor. Ver [cadeias de caracteres de finalidade](xref:security/data-protection/consumer-apis/purpose-strings) para muito mais informações sobre a intenção desse parâmetro e como escolher um valor apropriado.

## <a name="idataprotector"></a>IDataProtector

A interface de protetor é retornada por uma chamada para `CreateProtector`e ele essa interface que os consumidores podem usar para executar e proteger e Desproteger as operações.

Para proteger uma parte dos dados, passe os dados para o `Protect` método. A interface básica define um método que byte converte [] -> byte [], mas há também uma sobrecarga (fornecida como um método de extensão), que converte a cadeia de caracteres -> a cadeia de caracteres. A segurança oferecida pelos dois métodos é idêntica; o desenvolvedor deve escolher qualquer sobrecarga é mais conveniente para o caso de uso. Independentemente da sobrecarga escolhida, o valor retornado por proteger método agora é protegido (adulterem e à prova de adulteração) e o aplicativo pode enviar para um cliente não confiável.

Para desproteger uma parte anteriormente protegidos dos dados, passe os dados protegidos para o `Unprotect` método. (Há byte []-sobrecargas com base e baseada em cadeia de caracteres para conveniência do desenvolvedor.) Se o conteúdo protegido foi gerado por uma chamada anterior para `Protect` nos mesmos `IDataProtector`, o `Unprotect` método retornará a carga desprotegida original. Se a carga protegida foram violada ou foi produzida por outro `IDataProtector`, o `Unprotect` método gerará CryptographicException.

O conceito de mesma versus diferentes `IDataProtector` empates de volta para o conceito de finalidade. Se duas `IDataProtector` instâncias geradas a partir a mesma raiz `IDataProtectionProvider` mas por meio de cadeias de caracteres de finalidade diferente na chamada para `IDataProtectionProvider.CreateProtector`, em seguida, eles são considerados [protetores diferentes](xref:security/data-protection/consumer-apis/purpose-strings), e um não será possível desproteger conteúdos gerados pelo outro.

## <a name="consuming-these-interfaces"></a>Consumir essas interfaces

Para um componente de reconhecimento de DI, o uso pretendido é que o componente de levar um `IDataProtectionProvider` parâmetro em seu construtor e que o sistema de DI fornece automaticamente esse serviço quando o componente é instanciado.

> [!NOTE]
> Alguns aplicativos (como aplicativos de console ou aplicativos do ASP.NET 4.x) podem não ser reconhecimento de DI para que não é possível usar o mecanismo descrito aqui. Para esses cenários, confira a [não cenários de reconhecimento de DI](xref:security/data-protection/configuration/non-di-scenarios) documento para obter mais informações sobre como obter uma instância de um `IDataProtection` provedor sem passar por meio da DI.

O exemplo a seguir demonstra três conceitos:

1. [Adicione o sistema de proteção de dados](xref:security/data-protection/configuration/overview) ao contêiner de serviço,

2. Usando a DI para receber uma instância de um `IDataProtectionProvider`, e

3. Criando um `IDataProtector` de um `IDataProtectionProvider` e usá-lo para proteger e Desproteger dados.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

O pacote Microsoft.AspNetCore.DataProtection.Abstractions contém um método de extensão `IServiceProvider.GetDataProtector` como uma conveniência do desenvolvedor. Ele encapsula como uma única operação ambos os recuperar um `IDataProtectionProvider` do provedor de serviços e chamar `IDataProtectionProvider.CreateProtector`. O exemplo a seguir demonstra seu uso.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instâncias do `IDataProtectionProvider` e `IDataProtector` sejam thread-safe para chamadores vários. Ele deve que depois que um componente obtém uma referência a um `IDataProtector` por meio de uma chamada para `CreateProtector`, ele usará essa referência para várias chamadas para `Protect` e `Unprotect`. Uma chamada para `Unprotect` lançará CryptographicException se o conteúdo protegido não pode ser verificado ou decifrado. Alguns componentes talvez queira ignorar erros durante a Desproteger operações; um componente que lê os cookies de autenticação pode manipular esse erro e tratar a solicitação como se ele não tinha nenhum cookie em todos os em vez de falhar a solicitação imediatamente. Componentes que desejam esse comportamento devem especificamente capturar CryptographicException em vez de assimilação de todas as exceções.
