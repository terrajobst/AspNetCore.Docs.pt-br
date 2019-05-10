---
title: Suporte a política de todo o computador de proteção de dados no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o suporte para definir uma política de todo o computador padrão para todos os aplicativos que consomem a proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 70aaca7afcd3df22cebb4466fbd9845a2277688c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897263"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Suporte a política de todo o computador de proteção de dados no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Quando em execução no Windows, o sistema de proteção de dados tem suporte limitado para definir uma política de todo o computador padrão para todos os aplicativos que consomem a proteção de dados do ASP.NET Core. A ideia geral é que um administrador pode querer alterar uma configuração padrão, como os algoritmos usados ou a vida útil da chave, sem a necessidade de atualizar manualmente cada aplicativo no computador.

> [!WARNING]
> O administrador do sistema pode definir a política padrão, mas eles não não obrigatório. O desenvolvedor do aplicativo sempre pode substituir qualquer valor com uma das suas próprias escolhendo. A política padrão afeta apenas aplicativos em que o desenvolvedor não foi especificado um valor explícito para uma configuração.

## <a name="setting-default-policy"></a>Configuração de política padrão

Para definir a política padrão, um administrador pode definir os valores conhecidos no registro do sistema na seguinte chave do registro:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Se você estiver em um sistema operacional de 64 bits e deseja afetar o comportamento de aplicativos de 32 bits, lembre-se de configurar o equivalente a Wow6432Node a chave acima.

Os valores com suporte são mostrados abaixo.

| Valor              | Tipo   | Descrição |
| ------------------ | :----: | ----------- |
| EncryptionType     | cadeia de caracteres | Especifica quais algoritmos devem ser usados para proteção de dados. O valor deve ser CBC CNG, GCM CNG ou gerenciado e é descrito mais detalhadamente abaixo. |
| DefaultKeyLifetime | DWORD  | Especifica o tempo de vida para as chaves recentemente geradas. O valor é especificado em dias e deve ser > = 7. |
| KeyEscrowSinks     | cadeia de caracteres | Especifica os tipos que são usados para caução de chaves. O valor é uma lista delimitada por ponto e vírgula de Coletores de caução de chave, onde cada elemento na lista é o nome qualificado pelo assembly de um tipo que implementa [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Tipos de criptografia

Se EncryptionType é CBC CNG, o sistema está configurado para usar uma codificação de bloco simétrica de modo CBC para confidencialidade e HMAC para autenticidade com os serviços oferecidos pela CNG do Windows (consulte [especificando algoritmos personalizados de Windows CNG](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) para mais detalhes). Os seguintes valores adicionais são suportados, cada um dos quais corresponde a uma propriedade no tipo CngCbcAuthenticatedEncryptionSettings.

| Valor                       | Tipo   | Descrição |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | cadeia de caracteres | O nome de um algoritmo de criptografia de bloco simétricas compreendido pela CNG. Esse algoritmo é aberto no modo CBC. |
| EncryptionAlgorithmProvider | cadeia de caracteres | O nome da implementação do provedor CNG que pode produzir o algoritmo EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | O comprimento (em bits) da chave para derivar para o algoritmo de criptografia de bloco simétricas. |
| HashAlgorithm               | cadeia de caracteres | O nome de um algoritmo de hash compreendido pela CNG. Esse algoritmo é aberto no modo HMAC. |
| HashAlgorithmProvider       | cadeia de caracteres | O nome da implementação do provedor CNG que pode produzir o algoritmo HashAlgorithm. |

Se EncryptionType é CNG-GCM, o sistema está configurado para usar uma codificação de bloco simétrica de modo Galois/contador para a confidencialidade e a autenticidade com os serviços oferecidos pela CNG do Windows (consulte [especificando algoritmos personalizados de Windows CNG](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) Para obter mais detalhes). Os seguintes valores adicionais são suportados, cada um dos quais corresponde a uma propriedade no tipo CngGcmAuthenticatedEncryptionSettings.

| Valor                       | Tipo   | Descrição |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | cadeia de caracteres | O nome de um algoritmo de criptografia de bloco simétricas compreendido pela CNG. Esse algoritmo é aberto no modo de Galois/contador. |
| EncryptionAlgorithmProvider | cadeia de caracteres | O nome da implementação do provedor CNG que pode produzir o algoritmo EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | O comprimento (em bits) da chave para derivar para o algoritmo de criptografia de bloco simétricas. |

Se EncryptionType for gerenciado, o sistema está configurado para usar um SymmetricAlgorithm gerenciado para a confidencialidade e KeyedHashAlgorithm para autenticidade (consulte [especificação personalizada gerenciada algoritmos](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) para obter mais detalhes). Os seguintes valores adicionais são suportados, cada um dos quais corresponde a uma propriedade no tipo ManagedAuthenticatedEncryptionSettings.

| Valor                      | Tipo   | Descrição |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | cadeia de caracteres | O nome qualificado pelo assembly de um tipo que implementa SymmetricAlgorithm. |
| EncryptionAlgorithmKeySize | DWORD  | O comprimento (em bits) da chave para derivar o algoritmo de criptografia simétrica. |
| ValidationAlgorithmType    | cadeia de caracteres | O nome qualificado pelo assembly de um tipo que implementa KeyedHashAlgorithm. |

Se EncryptionType tiver qualquer outro valor diferente de nulo ou vazio, o sistema de proteção de dados gera uma exceção na inicialização.

> [!WARNING]
> Ao configurar uma configuração de política padrão que envolve os nomes de tipo (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), os tipos devem ser disponíveis para o aplicativo. Isso significa que para aplicativos em execução no CLR de área de trabalho, os assemblies que contêm esses tipos devem estar presentes no Cache de Assembly Global (GAC). Para aplicativos do ASP.NET Core em execução no .NET Core, os pacotes que contêm esses tipos devem ser instalados.
