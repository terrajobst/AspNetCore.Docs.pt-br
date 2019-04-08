---
title: Substitua o machineKey ASP.NET no ASP.NET Core
author: rick-anderson
description: Descubra como substituir machineKey no ASP.NET para permitir o uso de um sistema de proteção de dados novos e mais seguro.
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: ff36382d22a218a228b42a31ae4f8ad2eb2d5b5f
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068278"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Substitua o machineKey ASP.NET no ASP.NET Core

<a name="compatibility-replacing-machinekey"></a>

A implementação de `<machineKey>` elemento no ASP.NET [pode ser substituído pelo](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Isso permite que a maioria das chamadas para rotinas criptográficas do ASP.NET sejam roteadas por meio de um mecanismo de proteção de dados de substituição, incluindo o novo sistema de proteção de dados.

## <a name="package-installation"></a>Instalação do pacote

> [!NOTE]
> O novo sistema de proteção de dados só pode ser instalado em um aplicativo ASP.NET existente direcionado ao .NET 4.5.1 ou posterior. Instalação irá falhar se o aplicativo se destina ao .NET 4.5 ou inferior.

Para instalar o novo sistema de proteção de dados em um projeto de 4.5.1+ ASP.NET existente, instale o pacote Microsoft.AspNetCore.DataProtection.SystemWeb. Isso criará uma instância de sistema de proteção de dados usando o [configuração padrão](xref:security/data-protection/configuration/default-settings) configurações.

Quando você instala o pacote, ele insere uma linha em *Web. config* que informa ao usá-la para ASP.NET [mais operações criptográficas](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), incluindo autenticação de formulários, o estado de exibição e chamadas para MachineKey. A linha que está inserida lê da seguinte maneira.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Você pode informar se o novo sistema de proteção de dados estiver ativo por meio da inspeção campos como `__VIEWSTATE`, que deve começar com "CfDJ8" como no exemplo a seguir. "CfDJ8" é a representação de base64 do cabeçalho da mágica "09 F0 C9 F0" que identifica um conteúdo protegido pelo sistema de proteção de dados.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk..." />
```

## <a name="package-configuration"></a>configuração de pacote

O sistema de proteção de dados é instanciado com uma configuração de instalação de zero padrão. No entanto, já que, por padrão, as chaves são mantidas ao sistema de arquivos local, isso não funcionará para aplicativos que são implantados em um farm. Para resolver esse problema, você pode fornecer a configuração criando um tipo que herda DataProtectionStartup e substitui seu método ConfigureServices.

Abaixo está um exemplo de um tipo de inicialização de proteção de dados personalizados que configurado em que as chaves são persistidas e como eles são criptografados em repouso. Ele também substitui a política de isolamento de aplicativo padrão, fornecendo seu próprio nome do aplicativo.

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> Você também pode usar `<machineKey applicationName="my-app" ... />` no lugar de uma chamada explícita para SetApplicationName. Esse é um mecanismo de conveniência para evitar forçar o desenvolvedor a criar um tipo derivado de DataProtectionStartup se todos eles queriam para configurar o nome do aplicativo foi a configuração.

Para habilitar essa configuração personalizada, volte para a Web. config e procure o `<appSettings>` elemento que instala o pacote adicionado ao arquivo de configuração. Ele se parecerá com a seguinte marcação:

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

Preencha o valor em branco com o nome qualificado pelo assembly do tipo derivado de DataProtectionStartup que você acabou de criar. Se o nome do aplicativo for DataProtectionDemo, isso seria semelhante a abaixo.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

O sistema de proteção de dados recentemente configurado agora está pronto para uso dentro do aplicativo.
