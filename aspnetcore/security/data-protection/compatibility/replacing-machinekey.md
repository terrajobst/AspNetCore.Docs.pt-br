---
title: Substitua o ASP.NET machineKey no ASP.NET Core
author: rick-anderson
description: Descubra como substituir o machineKey em ASP.NET para permitir o uso de um sistema de proteção de dados novo e mais seguro.
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667982"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="7bcbe-103">Substitua o ASP.NET machineKey no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7bcbe-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="7bcbe-104">A implementação do elemento `<machineKey>` em ASP.NET [é substituível](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="7bcbe-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="7bcbe-105">Isso permite que a maioria das chamadas para rotinas de criptografia ASP.NET seja roteada por meio de um mecanismo de proteção de dados de substituição, incluindo o novo sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="7bcbe-106">Instalação do pacote</span><span class="sxs-lookup"><span data-stu-id="7bcbe-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="7bcbe-107">O novo sistema de proteção de dados só pode ser instalado em um aplicativo ASP.NET existente destinado ao .NET 4.5.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="7bcbe-108">A instalação falhará se o aplicativo for destinado ao .NET 4,5 ou inferior.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="7bcbe-109">Para instalar o novo sistema de proteção de dados em um projeto existente do ASP.NET 4.5.1 +, instale o pacote Microsoft. AspNetCore. dataprotection. SystemWeb.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="7bcbe-110">Isso criará uma instância do sistema de proteção de dados usando as definições de [configuração padrão](xref:security/data-protection/configuration/default-settings) .</span><span class="sxs-lookup"><span data-stu-id="7bcbe-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="7bcbe-111">Quando você instala o pacote, ele insere uma linha em *Web. config* que informa ao ASP.net para usá-lo para [a maioria das operações de criptografia](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), incluindo autenticação de formulários, estado de exibição e chamadas para machineKey. Protect.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="7bcbe-112">A linha que é inserida é lida da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="7bcbe-113">Você pode saber se o novo sistema de proteção de dados está ativo inspecionando campos como `__VIEWSTATE`, que devem começar com "CfDJ8", como no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="7bcbe-114">"CfDJ8" é a representação base64 do cabeçalho mágico "09 F0 C9 F0" que identifica uma carga protegida pelo sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="7bcbe-115">Configuração do pacote</span><span class="sxs-lookup"><span data-stu-id="7bcbe-115">Package configuration</span></span>

<span data-ttu-id="7bcbe-116">O sistema de proteção de dados é instanciado com uma configuração padrão de configuração zero.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="7bcbe-117">No entanto, como as chaves padrão são mantidas no sistema de arquivos local, isso não funcionará para aplicativos que são implantados em um farm.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="7bcbe-118">Para resolver isso, você pode fornecer a configuração criando um tipo que subclasses DataProtectionStartup e substitui seu método configuraservices.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="7bcbe-119">Abaixo está um exemplo de um tipo de inicialização de proteção de dados personalizado que configurou ambos os locais em que as chaves são mantidas e como elas são criptografadas em repouso.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="7bcbe-120">Ele também substitui a política de isolamento de aplicativo padrão fornecendo seu próprio nome de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

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
> <span data-ttu-id="7bcbe-121">Você também pode usar `<machineKey applicationName="my-app" ... />` no lugar de uma chamada explícita para setapplicationname.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="7bcbe-122">Esse é um mecanismo de conveniência para evitar forçar o desenvolvedor a criar um tipo derivado de DataProtectionStartup se tudo o que quisesse configurar estava definindo o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="7bcbe-123">Para habilitar essa configuração personalizada, volte para Web. config e procure o elemento `<appSettings>` que a instalação do pacote adicionou ao arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="7bcbe-124">Ele se parecerá com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="7bcbe-124">It will look like the following markup:</span></span>

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

<span data-ttu-id="7bcbe-125">Preencha o valor em branco com o nome qualificado pelo assembly do tipo derivado de DataProtectionStartup que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="7bcbe-126">Se o nome do aplicativo for DataProtectionDemo, isso será semelhante ao mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="7bcbe-127">O sistema de proteção de dados recém-configurado agora está pronto para uso dentro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bcbe-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
