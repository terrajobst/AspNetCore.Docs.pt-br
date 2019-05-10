---
title: Cenários de reconhecimento de DI não para proteção de dados no ASP.NET Core
author: rick-anderson
description: Saiba como dar suporte a cenários de proteção de dados em que você não pode ou não quiser usar um serviço fornecido pela injeção de dependência.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 34354c8443f6ae00bcce6ad9bdb6c11aaaa25bf8
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897303"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Cenários de reconhecimento de DI não para proteção de dados no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O sistema de proteção de dados do ASP.NET Core é normalmente [adicionado a um contêiner de serviço](xref:security/data-protection/consumer-apis/overview) e consumida pelos componentes dependentes por meio da injeção de dependência (DI). No entanto, há casos em que isso não é possível ou desejado, especialmente ao importar o sistema para um aplicativo existente.

Para dar suporte a esses cenários, o [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) pacote fornece um tipo concreto, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), que oferece uma maneira simples de usar a proteção de dados sem depender de injeção de dependência. O `DataProtectionProvider` tipo implementa [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). Construindo `DataProtectionProvider` requer apenas o fornecimento de uma [DirectoryInfo](/dotnet/api/system.io.directoryinfo) instância para indicar onde as chaves de criptografia do provedor devem ser armazenadas, conforme mostrado no exemplo de código a seguir:

[!code-none[](non-di-scenarios/_static/nodisample1.cs)]

Por padrão, o `DataProtectionProvider` tipo concreto não criptografa o material da chave bruto antes persisti-los para o sistema de arquivos. Isso serve para dar suporte a cenários em que os pontos de desenvolvedor para um compartilhamento de rede e o sistema de proteção de dados não é possível deduzir automaticamente um mecanismo de criptografia de chave apropriado em repouso.

Além disso, o `DataProtectionProvider` não do tipo concreto [isolar aplicativos](xref:security/data-protection/configuration/overview#per-application-isolation) por padrão. Todos os aplicativos usando o mesmo diretório de chave podem compartilhar cargas, desde que suas [parâmetros de propósito](xref:security/data-protection/consumer-apis/purpose-strings) corresponder.

O [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) construtor aceita um retorno de chamada de configuração opcionais que pode ser usado para ajustar os comportamentos do sistema. O exemplo a seguir demonstra o isolamento de restauração com uma chamada explícita para [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). O exemplo também demonstra a configuração do sistema para automaticamente criptografar chaves persistidas, usando a DPAPI do Windows. Se o diretório aponta para um compartilhamento UNC, você poderá distribuir um certificado compartilhado entre todos os computadores relevantes e configurar o sistema para usar a criptografia baseada em certificado com uma chamada para [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-none[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Instâncias do `DataProtectionProvider` tipo concreto são caros de criar. Se um aplicativo mantém várias instâncias desse tipo e se eles estão usando o mesmo diretório de armazenamento de chaves, pode prejudicar o desempenho do aplicativo. Se você usar o `DataProtectionProvider` tipo, é recomendável que você crie esse tipo de uma vez e reutilizá-la tanto quanto possível. O `DataProtectionProvider` tipo e todos os [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) instâncias criadas por meio dela são thread-safe para chamadores vários.
