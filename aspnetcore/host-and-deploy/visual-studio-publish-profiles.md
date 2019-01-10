---
title: Perfis de publicação do Visual Studio para a implantação do aplicativo ASP.NET Core
author: rick-anderson
description: Saiba como criar perfis de publicação no Visual Studio e usá-los para gerenciar implantações de aplicativo ASP.NET Core para vários destinos.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 3d24cd2cd4697e8e7cf7e4bdf4d076a09b6a6a23
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284702"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a>Perfis de publicação do Visual Studio para a implantação do aplicativo ASP.NET Core

Por [Sayed Hashimi de Ibrahim](https://github.com/sayedihashimi) e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="<= aspnetcore-1.1"

Para obter a versão 1.1 deste tópico, baixe [Perfis de publicação do Visual Studio para implantação de aplicativo do ASP.NET Core (versão 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/VS_Publish_Profiles_1.1.pdf).

::: moniker-end

Este documento se concentra no uso do Visual Studio 2017 (ou posterior) para criar e usar perfis de publicação. Os perfis de publicação criados com o Visual Studio podem ser executados do MSBuild e do Visual Studio. Consulte [Publicar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) para obter instruções sobre a publicação no Azure.

O arquivo de projeto a seguir foi criado com o comando `dotnet new mvc`:

::: moniker range=">= aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

O atributo `<Project>` do elemento `Sdk` realiza as seguintes tarefas:

* Importa o arquivo de propriedades de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* no início.
* Importa o arquivo de destino de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* no fim.

O local padrão para `MSBuildSDKsPath` (com o Visual Studio Enterprise 2017) é a pasta *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks*.

O SDK de `Microsoft.NET.Sdk.Web` depende de:

* *Microsoft.NET.Sdk.Web.ProjectSystem*
* *Microsoft.NET.Sdk.Publish*

O que faz com que as propriedades e os destinos a seguir a sejam importados:

* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props*
* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets*
* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props*
* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets*

Destinos de publicação importam o conjunto certo de destinos com base no método de publicação usado.

Quando o MSBuild ou o Visual Studio carrega um projeto, as seguintes ações de nível alto ocorrem:

* Compilar projeto
* Computar arquivos a publicar
* Publicar arquivos para o destino

## <a name="compute-project-items"></a>Itens de projeto de computação

Quando o projeto é carregado, os itens de projeto (arquivos) são computados. O atributo `item type` determina como o arquivo é processado. Por padrão, os arquivos *.cs* são incluídos na lista de itens `Compile`. Os arquivos na lista de itens `Compile` são compilados.

A lista de itens `Content` contém arquivos que são publicados juntamente com as saídas de build. Por padrão, os arquivos correspondendo ao padrão `wwwroot/**` são incluídos no item `Content`. O [padrão glob](https://gruntjs.com/configuring-tasks#globbing-patterns) `wwwroot/\*\*` corresponde a todos os arquivos na pasta *wwwroot* **e** subpastas. Para adicionar explicitamente um arquivo à lista de publicação, adicione o arquivo diretamente no arquivo *.csproj* conforme mostrado em [Arquivos de Inclusão](#include-files).

Ao selecionar o botão **Publicar** no Visual Studio ou ao publicar da linha de comando:

* Os itens/propriedades são calculados (os arquivos necessários para compilar).
* **Somente Visual Studio**: os pacotes do NuGet são restaurados. (A restauração precisa ser explícita pelo usuário na CLI.)
* O projeto é compilado.
* Os itens de publicação são computados (os arquivos necessários para a publicação).
* O projeto é publicado (os arquivos computados são copiados para o destino de publicação).

Quando um projeto do ASP.NET Core faz referência a `Microsoft.NET.Sdk.Web` no arquivo de projeto, um arquivo *app_offline.htm* é colocado na raiz do diretório do aplicativo Web. Quando o arquivo estiver presente, o módulo do ASP.NET Core apenas desligará o aplicativo e servirá o arquivo *app_offline.htm* durante a implantação. Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Publicação de linha de comando básica

A publicação de linha de comando funciona em todas as plataformas compatíveis com o .NET Core e não requer o Visual Studio. Nas amostras abaixo, o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) é executado no diretório do projeto (que contém o arquivo *.csproj*). Se você não estiver na pasta do projeto, passe explicitamente no caminho do arquivo de projeto. Por exemplo:

```console
dotnet publish C:\Webs\Web1
```

Execute os comandos a seguir para criar e publicar um aplicativo Web:

```console
dotnet new mvc
dotnet publish
```

O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) gera uma saída semelhante à seguinte:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.3.409.57025 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\publish\
```

A pasta de publicação padrão é `bin\$(Configuration)\netcoreapp<version>\publish`. O padrão para `$(Configuration)` é *Debug*. Na amostra anterior, o `<TargetFramework>` é `netcoreapp2.0`.

`dotnet publish -h` exibe informações de ajuda para publicação.

O comando a seguir especifica um build de `Release` e o diretório de publicação:

```console
dotnet publish -c Release -o C:\MyWebs\test
```

O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) chama MSBuild, que invoca o destino `Publish`. Quaisquer parâmetros passados para `dotnet publish` são passados para o MSBuild. O parâmetro `-c` é mapeado para a propriedade do MSBuild `Configuration`. O parâmetro `-o` é mapeado para `OutputPath`.

Propriedades do MSBuild podem ser passadas usando qualquer um dos seguintes formatos:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

O comando a seguir publica um build de `Release` para um compartilhamento de rede:

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

O compartilhamento de rede é especificado com barras "/" (*//r8/*) e funciona em todas as plataformas com suporte do .NET Core.

Confirme que o aplicativo publicado para implantação não está em execução. Os arquivos da pasta *publish* são bloqueados quando o aplicativo está em execução. A implantação não pode ocorrer porque arquivos bloqueados não podem ser copiados.

## <a name="publish-profiles"></a>Perfis de publicação

Esta seção usa o Visual Studio 2017 (ou posterior) para criar um perfil de publicação. Uma vez criado, é possível publicar do Visual Studio ou da linha de comando.

Perfis de publicação podem simplificar o processo de publicação e podem existir em qualquer número. Crie um perfil de publicação no Visual Studio escolhendo um dos seguintes caminhos:

* Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Publicar**.
* Selecione **Publicar {NOME DO PROJETO}** no menu **Build**.

A guia **Publicar** da página de capacidades do aplicativo é exibida. Se o projeto não tem um perfil de publicação, a página a seguir é exibida:

![A guia Publicar da página de capacidades do aplicativo](visual-studio-publish-profiles/_static/az.png)

Quando a opção **Pasta** é selecionada, especifique um caminho de pasta para armazenar os ativos publicados. A pasta padrão é *bin\Release\PublishOutput*. Clique no botão **Criar Perfil** para concluir.

Depois de um perfil de publicação ser criado, a guia **Publicar** é alterada. O perfil recém-criado é exibido em uma lista suspensa. Clique em **Criar novo perfil** para fazer isso.

![A guia Publicar da página de capacidades do aplicativo mostrando FolderProfile](visual-studio-publish-profiles/_static/create_new.png)

O Assistente de publicação dá suporte aos destinos de publicação a seguir:

* Serviço de Aplicativo do Azure
* Máquinas Virtuais do Azure
* IIS, FTP, etc. (para qualquer servidor Web)
* Pasta
* Importar Perfil

Para obter mais informações, confira [Quais opções de publicação são adequadas para mim](/visualstudio/ide/not-in-toc/web-publish-options).

Quando você cria um perfil de publicação com o Visual Studio, um arquivo do MSBuild *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml* é criado. O *.pubxml* é um arquivo do MSBuild e contém definições de configuração de publicação. Esse arquivo pode ser alterado para personalizar o processo de build e de publicação. Esse arquivo é lido pelo processo de publicação. `<LastUsedBuildConfiguration>` é especial porque é uma propriedade global e não deverá estar em nenhum arquivo importado no build. Para obter mais informações, confira [MSBuild: como definir a propriedade de configuração](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

Ao publicar em um destino do Azure, o arquivo *.pubxml* contém o identificador de assinatura do Azure. Com esse tipo de destino, não é recomendável adicionar esse arquivo ao controle do código-fonte. Ao publicar em um destino não Azure, é seguro fazer check-in do arquivo *.pubxml*.

Informações confidenciais (como a senha de publicação) são criptografadas em um nível por usuário/computador. Elas são armazenadas no arquivo *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml.user*. Já que esse arquivo pode armazenar informações confidenciais, o check-in dele não deve ser realizado no controle do código-fonte.

Para obter uma visão geral de como publicar um aplicativo Web do ASP.NET Core, veja [Hospedar e implantar](xref:host-and-deploy/index). As tarefas e os destinos de MSBuild necessários para publicar um aplicativo ASP.NET Core são de software livre no https://github.com/aspnet/websdk.

O `dotnet publish` pode usar os perfis de publicação de pasta, MSDeploy e [Kudu](https://github.com/projectkudu/kudu/wiki):

Pasta (funciona em modo multiplataforma):

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

MSDeploy (atualmente só funciona no Windows, uma vez que o MSDeploy não é multiplataforma):

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

Pacote MSDeploy (atualmente só funciona no Windows, uma vez que o MSDeploy não é multiplataforma):

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

Nos exemplos anteriores, **não** passe o `deployonbuild` para `dotnet publish`.

Para obter mais informações, confira [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).

O `dotnet publish` dá suporte às APIs do Kudu, para publicar no Azure de qualquer plataforma. A publicação do Visual Studio dá suporte às APIs do Kudu, mas ela é compatível com o WebSDK para publicação multiplataforma para o Azure.

Adicione um perfil de publicação à pasta *Properties/PublishProfiles* com o seguinte conteúdo:

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

Execute o seguinte comando para compactar os conteúdos de publicação e publicá-los no Azure usando as APIs do Kudu:

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

Defina as seguintes propriedades de MSBuild ao usar um perfil de publicação:

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

Ao publicar com um perfil chamado *FolderProfile*, é possível executar qualquer um dos comandos abaixo:

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

Ao invocar [dotnet build](/dotnet/core/tools/dotnet-build), ele chama `msbuild` para executar o processo de build e de publicação. Chamar `dotnet build` ou `msbuild` é equivalente ao passar um perfil de pasta. Ao chamar o MSBuild diretamente no Windows, a versão do .NET Framework do MSBuild é usada. O MSDeploy é atualmente limitado a computadores Windows para a publicação. Chamar `dotnet build` em um perfil não de pasta invoca o MSBuild que, por sua vez, usa MSDeploy em perfis não de pasta. Chamar `dotnet build` em um perfil não de pasta invoca o MSBuild (usando MSDeploy) e resulta em uma falha (mesmo quando em execução em uma plataforma do Windows). Para publicar com um perfil não de pasta, chame o MSBuild diretamente.

A pasta de perfil de publicação a seguir foi criada com o Visual Studio e publica em um compartilhamento de rede:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

Observe que `<LastUsedBuildConfiguration>` é definido como `Release`. Ao publicar no Visual Studio, o valor da propriedade de configuração `<LastUsedBuildConfiguration>` é definido usando o valor quando o processo de publicação é iniciado. A propriedade de configuração `<LastUsedBuildConfiguration>` é especial e não deve ser substituída em um arquivo do MSBuild importado. Essa propriedade pode ser substituída da linha de comando.

Usando a CLI do .NET Core:

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

Usando o MSBuild:

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Publicar em um ponto de extremidade do MSDeploy da linha de comando

O exemplo a seguir usa um aplicativo Web do ASP.NET Core, criado pelo Visual Studio, denominada *AzureWebApp*. Um perfil de publicação dos aplicativos do Azure é adicionado ao Visual Studio. Para obter mais informações sobre como criar um perfil, consulte a seção [Perfis de publicação](#publish-profiles).

Para implantar o aplicativo usando um perfil de publicação, execute o comando `msbuild` de um **Prompt de Comando do Desenvolvedor** do Visual Studio. O prompt de comando está disponível na pasta do *Visual Studio* do menu **Página Inicial** na barra de tarefas do Windows. Para facilitar o acesso, você pode adicionar o prompt de comando ao menu **Ferramentas** no Visual Studio. Para saber mais, confira [Prompt de comando do desenvolvedor para Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

O MSBuild usa a sintaxe de comando a seguir:

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* {CAMINHO} &ndash; Caminho para o arquivo de projeto do aplicativo.
* {PERFIL} &ndash; Nome do perfil de publicação.
* {NOME DE USUÁRIO} &ndash; Nome de usuário do MSDeploy. O {NOME DE USUÁRIO} pode ser encontrado no perfil de publicação.
* {SENHA} &ndash; Senha do MSDeploy. Obtenha a {SENHA} do arquivo *{PERFIL}.PublishSettings*. Baixe o arquivo *.PublishSettings* de uma das seguintes opções:
  * Gerenciador de Soluções: Selecione **Exibição** > **Cloud Explorer**. Conecte-se à sua assinatura do Azure. Abra **Serviços de Aplicativos**. Clique com o botão direito do mouse no aplicativo. Selecione **Baixar Perfil de Publicação**.
  * Portal do Azure: selecione **Obter perfil de publicação** no painel **Visão geral** de seu aplicativo Web.

O exemplo a seguir usa um perfil de publicação denominado *AzureWebApp – Implantação da Web*:

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Um perfil de publicação também pode ser usado com o comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) da CLI do .NET Core em um prompt de comando do Windows:

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> O comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) está disponível em várias plataformas e pode compilar aplicativos ASP.NET Core no macOS e Linux. No entanto, o MSBuild no macOS e Linux não é capaz de implantar um aplicativo no Azure ou em outro ponto de extremidade do MSDeploy. O MSDeploy está disponível apenas no Windows.

## <a name="exclude-files"></a>Excluir arquivos

Ao publicar aplicativos Web do ASP.NET Core, os artefatos de build e o conteúdo da pasta *wwwroot* são incluídos. `msbuild` dá suporte a [padrões de caractere curinga](https://gruntjs.com/configuring-tasks#globbing-patterns). Por exemplo, o elemento `<Content>` a seguir exclui todos os arquivos de texto (*.txt*) da pasta *wwwroot/content* e de todas as suas subpastas.

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

A marcação anterior pode ser adicionada a um perfil de publicação ou ao arquivo *.csproj*. Quando adicionada ao arquivo *.csproj*, a regra será adicionada a todos os perfis de publicação no projeto.

O elemento `<MsDeploySkipRules>` a seguir exclui todos os arquivos da pasta *wwwroot/content*:

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` não exclui os destinos de *ignorados* do site de implantação. Pastas e arquivos de destino de `<Content>` são excluídos do site de implantação. Por exemplo, suponha que um aplicativo Web implantado tinha os seguintes arquivos:

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

Nos elementos `<MsDeploySkipRules>` a seguir, esses arquivos não seriam excluídos no site de implantação.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Os elementos `<MsDeploySkipRules>` anteriores impedem que os arquivos *ignorados* sejam implantados. Ele não excluirá esses arquivos depois que eles forem implantados.

O elemento `<Content>` a seguir exclui os arquivos de destino no site de implantação:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

O uso da implantação de linha de comando com o elemento `<Content>` anterior produz a seguinte saída:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>Incluir arquivos

A marcação a seguir inclui uma pasta *images* fora do diretório do projeto para a pasta *wwwroot/images* do site de publicação:

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

A marcação pode ser adicionada ao arquivo *.csproj* ou ao perfil de publicação. Se ela for adicionada ao arquivo *.csproj*, ela será incluída em cada perfil de publicação no projeto.

A marcação realçada a seguir mostra como:

* Copiar um arquivo de fora do projeto para a pasta *wwwroot*.
* Excluir a pasta *wwwroot\Content*.
* Excluir *Views\Home\About2.cshtml*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

Consulte o [Leiame do WebSDK](https://github.com/aspnet/websdk) para obter mais amostras de implantação.

## <a name="run-a-target-before-or-after-publishing"></a>Executar um destino antes ou depois da publicação

Os destinos `BeforePublish` e `AfterPublish` internos executam um destino antes ou após o destino de publicação. Adicione os elementos a seguir ao perfil de publicação para registrar em log as mensagens de console antes e após a publicação:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Publicar em um servidor usando um certificado não confiável

Adicione a propriedade `<AllowUntrustedCertificate>` com um valor `True` ao perfil de publicação:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>O serviço Kudu

Para exibir os arquivos em uma implantação de aplicativo Web do Serviço de Aplicativo do Azure, use o [serviço Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Acrescente o token `scm` ao nome do aplicativo Web. Por exemplo:

| URL                                    | Resultado       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Aplicativo Web      |
| `http://mysite.scm.azurewebsites.net/` | Serviço Kudu |

Selecione o item de menu [Console de Depuração](https://github.com/projectkudu/kudu/wiki/Kudu-console) para exibir, editar, excluir ou adicionar arquivos.

## <a name="additional-resources"></a>Recursos adicionais

* A [Implantação da Web](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifica a implantação de aplicativos Web e sites da Web em servidores IIS.
* [https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): problemas de arquivos e recursos de solicitação para implantação.
* [Publicar um aplicativo Web ASP.NET em uma VM do Azure usando o Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
