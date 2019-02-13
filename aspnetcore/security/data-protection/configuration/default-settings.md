---
title: Gerenciamento de chaves de proteção de dados e o tempo de vida no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o gerenciamento de chaves de proteção de dados e o tempo de vida no ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 2f022a4c7519485fe629ce47c27d214c8c27d5bc
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159205"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Gerenciamento de chaves de proteção de dados e o tempo de vida no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Gerenciamento de chaves

O aplicativo tenta detectar seu ambiente operacional e lidar com a configuração de chave por conta própria.

1. Se o aplicativo estiver hospedado em [aplicativos do Azure](https://azure.microsoft.com/services/app-service/), as chaves são persistidas para o *%HOME%\ASP.NET\DataProtection-Keys* pasta. Essa pasta é apoiada pelo repositório de rede e é sincronizada em todos os computadores que hospedam o aplicativo.
   * As chaves não são protegidas em repouso.
   * O *DataProtection chaves* pasta fornece o anel de chave a todas as instâncias de um aplicativo em um único slot de implantação.
   * Slots de implantação separados, como de preparo e produção, não compartilham um anel de chave. Quando você alternar entre os slots de implantação, por exemplo, trocar o preparo e produção ou usando um teste a / B, qualquer aplicativo usando a proteção de dados não será capaz de descriptografar dados armazenados usando o anel de chave dentro do slot anterior. Isso leva a usuários que estão sendo conectados fora de um aplicativo que usa a autenticação de cookie padrão do ASP.NET Core, pois ele usa a proteção de dados para proteger seus cookies. Se desejar que os anéis de chave independente de slot, use um provedor de anel de chave externo, como o armazenamento de BLOBs Azure, Azure Key Vault, um repositório SQL ou do cache Redis.

1. Se o perfil do usuário estiver disponível, as chaves são persistidas para o *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* pasta. Se o sistema operacional for Windows, as chaves são criptografadas em repouso usando a DPAPI.

   O pool de aplicativos [setProfileEnvironment atributo](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) também deve ser habilitado. O valor padrão de `setProfileEnvironment` é `true`. Em alguns cenários (por exemplo, SO Windows), `setProfileEnvironment` é definido como `false`. Se as chaves não são armazenadas no diretório de perfil do usuário, como esperado:

   1. Navegue até a *%windir%/system32/inetsrv/config* pasta.
   1. Abra o *applicationHost. config* arquivo.
   1. Localize o elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
   1. Confirme se o `setProfileEnvironment` atributo não estiver presente, cujo padrão é o valor para `true`, ou defina explicitamente o valor do atributo `true`.

1. Se o aplicativo estiver hospedado no IIS, as chaves são mantidas no registro HKLM em uma chave de registro especial que tem a ACL acessível apenas para a conta de processo de trabalho. As chaves são criptografadas em repouso usando a DPAPI.

1. Se nenhuma dessas condições corresponderem, as chaves não são persistidas fora do processo atual. Quando o processo é encerrado, todas geradas chaves forem perdidas.

O desenvolvedor está sempre no controle total e pode substituir como e onde as chaves são armazenadas. As três primeiras opções acima devem fornecer bons padrões para a maioria dos aplicativos de forma semelhante a como o ASP.NET  **\<machineKey >** rotinas de geração automática funcionavam no passado. A opção de fallback final é o único cenário que exige que o desenvolvedor especifique [configuração](xref:security/data-protection/configuration/overview) antecipado se quiserem persistência de chave, mas essa fallback só ocorre em situações raras.

Ao hospedar em um contêiner do Docker, as chaves devem ser persistentes em uma pasta que é um volume do Docker (um volume compartilhado ou um volume montado de host que persiste além do tempo de vida do contêiner) ou em um provedor externo, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou [Redis](https://redis.io/). Um provedor externo também é útil em cenários de web farm se os aplicativos não podem acessar um volume compartilhado de rede (consulte [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) para obter mais informações).

> [!WARNING]
> Se o desenvolvedor substitui as regras descritas acima e aponta o sistema de proteção de dados em um repositório de chave específico, a criptografia automática de chaves em repouso está desabilitada. Proteção em repouso pode ser reabilitada por meio [configuração](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Vida útil da chave

Por padrão, as chaves têm um tempo de vida de 90 dias. Quando uma chave expira, o aplicativo gera uma nova chave automaticamente e define a nova chave como a chave ativa. Chaves obsoletos permanecerão no sistema, desde que seu aplicativo pode descriptografar todos os dados protegidos com eles. Ver [gerenciamento de chaves](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) para obter mais informações.

## <a name="default-algorithms"></a>Algoritmos padrão

O algoritmo de proteção de conteúdo padrão usado é AES-256-CBC para confidencialidade e HMACSHA256 autenticidade. Uma chave mestra de 512 bits, alterada a cada 90 dias, é usada para derivar as duas chaves de subpropriedades usadas para esses algoritmos em uma base por carga. Ver [subchave derivação](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) para obter mais informações.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
