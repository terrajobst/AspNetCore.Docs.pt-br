---
title: Suporte de política de toda a máquina de proteção de dados no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o suporte para definir uma política padrão em todo o computador para todos os aplicativos que consomem ASP.NET Core proteção de dados.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 70aaca7afcd3df22cebb4466fbd9845a2277688c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667947"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Suporte de política de toda a máquina de proteção de dados no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Ao executar no Windows, o sistema de proteção de dados tem suporte limitado para definir uma política padrão em todo o computador para todos os aplicativos que consomem ASP.NET Core proteção de dados. A ideia geral é que um administrador talvez queira alterar uma configuração padrão, como os algoritmos usados ou o tempo de vida da chave, sem a necessidade de atualizar manualmente todos os aplicativos no computador.

> [!WARNING]
> O administrador do sistema pode definir a política padrão, mas não pode imaplicá-la. O desenvolvedor do aplicativo sempre pode substituir qualquer valor por um de sua escolha. A política padrão afeta apenas os aplicativos em que o desenvolvedor não especificou um valor explícito para uma configuração.

## <a name="setting-default-policy"></a>Definindo a política padrão

Para definir a política padrão, um administrador pode definir valores conhecidos no registro do sistema na seguinte chave do registro:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Se você estiver em um sistema operacional de 64 bits e quiser afetar o comportamento dos aplicativos de 32 bits, lembre-se de configurar o equivalente Wow6432Node da chave acima.

Os valores com suporte são mostrados abaixo.

| {1&gt;Valor&lt;1}              | Tipo   | Descrição |
| ------------------ | :----: | ----------- |
| EncryptionType     | string | Especifica quais algoritmos devem ser usados para proteção de dados. O valor deve ser CNG-CBC, CNG-GCM ou gerenciado e é descrito mais detalhadamente abaixo. |
| DefaultKeyLifetime | DWORD  | Especifica o tempo de vida para chaves geradas recentemente. O valor é especificado em dias e deve ser > = 7. |
| KeyEscrowSinks     | string | Especifica os tipos que são usados para a caução de chave. O valor é uma lista delimitada por ponto-e-vírgula de coletores de caução de chave, em que cada elemento na lista é o nome qualificado do assembly de um tipo que implementa [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Tipos de criptografia

Se EncryptionType for CNG-CBC, o sistema será configurado para usar uma codificação de bloco simétrico do modo CBC para confidencialidade e HMAC para autenticidade com os serviços fornecidos pela CNG do Windows (consulte [especificando algoritmos personalizados de CNG do Windows](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) para obter mais detalhes). Há suporte para os seguintes valores adicionais, sendo que cada um corresponde a uma propriedade no tipo CngCbcAuthenticatedEncryptionSettings.

| {1&gt;Valor&lt;1}                       | Tipo   | Descrição |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | O nome de um algoritmo de codificação de bloco simétrico compreendido pela CNG. Esse algoritmo é aberto no modo CBC. |
| EncryptionAlgorithmProvider | string | O nome da implementação do provedor CNG que pode produzir o algoritmo EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | O comprimento (em bits) da chave para derivar para o algoritmo de codificação de bloco simétrico. |
| HashAlgorithm               | string | O nome de um algoritmo de hash compreendido pela CNG. Esse algoritmo é aberto no modo HMAC. |
| HashAlgorithmProvider       | string | O nome da implementação do provedor CNG que pode produzir o algoritmo HashAlgorithm. |

Se EncryptionType for CNG-GCM, o sistema será configurado para usar uma codificação de bloco simétrico do modo Galois/Counter para confidencialidade e autenticidade com os serviços fornecidos pela CNG do Windows (consulte [especificando algoritmos personalizados de CNG do Windows](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) para obter mais detalhes). Há suporte para os seguintes valores adicionais, sendo que cada um corresponde a uma propriedade no tipo CngGcmAuthenticatedEncryptionSettings.

| {1&gt;Valor&lt;1}                       | Tipo   | Descrição |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | O nome de um algoritmo de codificação de bloco simétrico compreendido pela CNG. Esse algoritmo é aberto no modo Galois/Counter. |
| EncryptionAlgorithmProvider | string | O nome da implementação do provedor CNG que pode produzir o algoritmo EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | O comprimento (em bits) da chave para derivar para o algoritmo de codificação de bloco simétrico. |

Se EncryptionType for gerenciado, o sistema será configurado para usar um SymmetricAlgorithm gerenciado para confidencialidade e KeyedHashAlgorithm para autenticidade (consulte [especificando algoritmos gerenciados personalizados](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) para obter mais detalhes). Há suporte para os seguintes valores adicionais, sendo que cada um corresponde a uma propriedade no tipo ManagedAuthenticatedEncryptionSettings.

| {1&gt;Valor&lt;1}                      | Tipo   | Descrição |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | string | O nome qualificado pelo assembly de um tipo que implementa SymmetricAlgorithm. |
| EncryptionAlgorithmKeySize | DWORD  | O comprimento (em bits) da chave para derivar para o algoritmo de criptografia simétrica. |
| ValidationAlgorithmType    | string | O nome qualificado pelo assembly de um tipo que implementa KeyedHashAlgorithm. |

Se EncryptionType tiver qualquer outro valor diferente de NULL ou Empty, o sistema de proteção de dados lançará uma exceção na inicialização.

> [!WARNING]
> Ao configurar uma configuração de política padrão que envolve nomes de tipo (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), os tipos devem estar disponíveis para o aplicativo. Isso significa que para aplicativos em execução no CLR de área de trabalho, os assemblies que contêm esses tipos devem estar presentes no GAC (cache de assembly global). Para ASP.NET Core aplicativos em execução no .NET Core, os pacotes que contêm esses tipos devem ser instalados.
