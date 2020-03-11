---
title: Tempo de vida e gerenciamento de chaves de proteção de dados no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o gerenciamento de chaves de proteção de dados e o tempo de vida em ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 2f022a4c7519485fe629ce47c27d214c8c27d5bc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667961"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Tempo de vida e gerenciamento de chaves de proteção de dados no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Gerenciamento de chaves

O aplicativo tenta detectar seu ambiente operacional e manipular a configuração da chave por conta própria.

1. Se o aplicativo estiver hospedado em [aplicativos do Azure](https://azure.microsoft.com/services/app-service/), as chaves serão mantidas na pasta *%Home%\ASP.NET\DataProtection-Keys* . Essa pasta é apoiada pelo repositório de rede e é sincronizada em todos os computadores que hospedam o aplicativo.
   * As chaves não são protegidas em repouso.
   * A pasta *dataprotection-Keys* fornece o anel de chave para todas as instâncias de um aplicativo em um único slot de implantação.
   * Slots de implantação separados, como de preparo e produção, não compartilham um anel de chave. Quando você alterna entre os slots de implantação, por exemplo, trocando preparo para produção ou usando testes A/B, qualquer aplicativo usando a proteção de dados não será capaz de descriptografar dados armazenados usando o anel de chave dentro do slot anterior. Isso leva aos usuários que estão sendo desconectados de um aplicativo que usa a autenticação de cookie de ASP.NET Core padrão, pois ele usa a proteção de dados para proteger seus cookies. Se você quiser anéis de chave independentes de slot, use um provedor de anel de chave externa, como o armazenamento de BLOBs do Azure, Azure Key Vault, um repositório SQL ou cache Redis.

1. Se o perfil do usuário estiver disponível, as chaves serão mantidas na pasta *%LocalAppData%\ASP.NET\DataProtection-Keys* . Se o sistema operacional for Windows, as chaves serão criptografadas em repouso usando DPAPI.

   O [atributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) do pool de aplicativos também deve ser habilitado. O valor padrão de `setProfileEnvironment` é `true`. Em alguns cenários (por exemplo, um SO Windows), `setProfileEnvironment` é definido como `false`. Se as chaves não estiverem armazenadas no diretório do perfil do usuário como esperado:

   1. navegue até a pasta *%windir%/system32/inetsrv/config*.
   1. Abra o arquivo *applicationHost.config*.
   1. Localize o elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
   1. Confirme se o atributo `setProfileEnvironment` não está presente, que tem como padrão o valor `true`, ou defina explicitamente o valor do atributo como `true`.

1. Se o aplicativo estiver hospedado no IIS, as chaves serão mantidas no registro HKLM em uma chave de registro especial que é ACLed apenas para a conta de processo de trabalho. As chaves são criptografadas em repouso usando a DPAPI.

1. Se nenhuma dessas condições corresponder, as chaves não serão mantidas fora do processo atual. Quando o processo é desligado, todas as chaves geradas são perdidas.

O desenvolvedor está sempre no controle total e pode substituir como e onde as chaves são armazenadas. As três primeiras opções acima devem fornecer bons padrões para a maioria dos aplicativos semelhantes a como o ASP.NET **\<machineKey >** rotinas de geração automática funcionaram no passado. A opção final, fallback é o único cenário que exige que o desenvolvedor especifique a [configuração](xref:security/data-protection/configuration/overview) antecipada se quiser a persistência da chave, mas esse fallback só ocorre em situações raras.

Ao hospedar em um contêiner do Docker, as chaves devem ser mantidas em uma pasta que seja um volume do Docker (um volume compartilhado ou um volume montado pelo host que persiste além do tempo de vida do contêiner) ou em um provedor externo, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou [Redis](https://redis.io/). Um provedor externo também será útil em cenários de web farm se os aplicativos não puderem acessar um volume de rede compartilhada (consulte [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) para obter mais informações).

> [!WARNING]
> Se o desenvolvedor substituir as regras descritas acima e apontar o sistema de proteção de dados em um repositório de chaves específico, a criptografia automática de chaves em repouso será desabilitada. A proteção em repouso pode ser habilitada novamente por meio da [configuração](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Tempo de vida da chave

As chaves têm um tempo de vida de 90 dias por padrão. Quando uma chave expira, o aplicativo gera automaticamente uma nova chave e define a nova chave como a chave ativa. Desde que as chaves desativadas permaneçam no sistema, seu aplicativo poderá descriptografar os dados protegidos com eles. Consulte [Gerenciamento de chaves](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) para obter mais informações.

## <a name="default-algorithms"></a>Algoritmos padrão

O algoritmo de proteção de carga padrão usado é o AES-256-CBC para confidencialidade e HMACSHA256 para autenticidade. Uma chave mestra de 512 bits, alterada a cada 90 dias, é usada para derivar as duas subchaves usadas para esses algoritmos em uma base por carga. Consulte [derivação de subchave](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) para obter mais informações.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
