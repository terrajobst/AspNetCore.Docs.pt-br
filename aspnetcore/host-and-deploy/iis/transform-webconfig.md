---
title: Transformação do Web.config
author: rick-anderson
description: Saiba como transformar o arquivo web.config ao publicar um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 069b9bb516644a1a722235b33d4916460488ebf2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657930"
---
# <a name="transform-webconfig"></a>Transformação do Web.config

Por [Vijay Ramakrishnan](https://github.com/vijayrkn)

As transformações no arquivo *web.config* podem ser aplicadas automaticamente quando um aplicativo é publicado com base em:

* [Configuração do build](#build-configuration)
* [Perfil](#profile)
* [Ambiente](#environment)
* [Custom](#custom)

Essas transformações ocorrem para qualquer um dos seguintes cenários de geração *web.config*:

* Gerado automaticamente pelo SDK `Microsoft.NET.Sdk.Web`.
* Fornecido pelo desenvolvedor na raiz de [conteúdo](xref:fundamentals/index#content-root) do aplicativo.

## <a name="build-configuration"></a>Configurações de compilação

Transformações de configuração de compilação são executadas primeiros.

Inclua um arquivo *web.{CONFIGURATION}.config* para cada [configuração de build (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) que exija uma transformação de *web.config*.

No exemplo a seguir, uma variável de ambiente específica à configuração está definida em *web.Release.config*:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

A transformação é aplicada quando a configuração é definida como *Release*:

```dotnetcli
dotnet publish --configuration Release
```

A propriedade de MSBuild para a configuração é `$(Configuration)`.

## <a name="profile"></a>Perfil

As transformações de perfil são executadas depois, após as transformações da [Configuração de build](#build-configuration).

Inclua um arquivo *web.{PROFILE}.config* para cada configuração de perfil que exija uma transformação de *web.config*.

No exemplo a seguir, uma variável de ambiente específica ao perfil está definida em *web.FolderProfile.config* para um perfil de publicação de pasta:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

A transformação é aplicada quando o perfil for *FolderProfile*:

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

A propriedade de MSBuild para o nome do perfil é `$(PublishProfile)`.

Se nenhum perfil for passado, o nome do perfil padrão será **FileSystem** e *web. FileSystem.config* será aplicado se o arquivo estiver presente na raiz de conteúdo do aplicativo.

## <a name="environment"></a>Ambiente

As transformações de ambiente são executadas logo após as transformações da [Configuração de build](#build-configuration) e de [Perfil](#profile).

Inclua um arquivo *web.{ENVIRONMENT}.config* para cada [ambiente](xref:fundamentals/environments) que exija uma transformação de *web.config*.

No exemplo a seguir, uma variável de ambiente específica ao ambiente é definida em *web.Production.config* para o ambiente de Produção:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

A transformação será aplicada quando o ambiente for *Produção*:

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

A propriedade de MSBuild para o ambiente é `$(EnvironmentName)`.

Ao publicar no Visual Studio, usando um perfil de publicação, consulte <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.

A variável de ambiente `ASPNETCORE_ENVIRONMENT` é adicionada automaticamente ao arquivo *web.config* quando o nome do ambiente for especificado.

## <a name="custom"></a>Personalizado

As transformações personalizadas são executadas por último, após as transformações da [Configuração de build](#build-configuration), de [Perfil](#profile) e de [Ambiente](#environment).

Inclua um arquivo *{CUSTOM_NAME}.transform* para cada configuração personalizada que exija uma transformação de *web.config*.

No exemplo a seguir, uma variável de ambiente de transformação personalizada está definida em *custom.transform*:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

A transformação é aplicada quando a propriedade `CustomTransformFileName` é passada para o comando [dotnet publish](/dotnet/core/tools/dotnet-publish):

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

A propriedade de MSBuild para o nome do perfil é `$(CustomTransformFileName)`.

## <a name="prevent-webconfig-transformation"></a>Impedir a transformação de web.config

Para impedir transformações do arquivo *web.config*, defina a propriedade de MSBuild `$(IsWebConfigTransformDisabled)`:

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Recursos adicionais

* [Sintaxe de transformação de web.config para implantação de projeto de aplicativo Web](/previous-versions/dd465326(v=vs.100))
* [Sintaxe de transformação de web.config para implantação de projeto da Web usando o Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))
