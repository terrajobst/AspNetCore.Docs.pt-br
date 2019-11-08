---
title: Perfis de publicação do Visual Studio (. pubxml) para implantação de aplicativo ASP.NET Core
author: rick-anderson
description: Saiba como criar perfis de publicação no Visual Studio e usá-los para gerenciar implantações de aplicativo ASP.NET Core para vários destinos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 274dd2cd528d3766aa07f69aac3470a131c79ffe
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799346"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Perfis de publicação do Visual Studio (. pubxml) para implantação de aplicativo ASP.NET Core

Por [Sayed Hashimi de Ibrahim](https://github.com/sayedihashimi) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este documento se concentra no uso do Visual Studio 2019 ou posterior para criar e usar perfis de publicação. Os perfis de publicação criados com o Visual Studio podem ser usados com o MSBuild e o Visual Studio. Para obter instruções sobre a publicação no Azure, confira <xref:tutorials/publish-to-azure-webapp-using-vs>.

O comando `dotnet new mvc` produz um arquivo de projeto contendo o seguinte nível de raiz [\<Projeto> elemento](/visualstudio/msbuild/project-element-msbuild):

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

O atributo `Sdk` do elemento `<Project>` anterior importa as [propriedades](/visualstudio/msbuild/msbuild-properties) e os [destinos](/visualstudio/msbuild/msbuild-targets) do MSBuild de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* e *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectivamente. O local padrão para `$(MSBuildSDKsPath)` (com o Visual Studio 2019 Enterprise) é a pasta *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.

`Microsoft.NET.Sdk.Web` (SDK da Web) depende de outros SDKs, incluindo `Microsoft.NET.Sdk` (SDK do .NET Core) e `Microsoft.NET.Sdk.Razor` ([SDK do Razor](xref:razor-pages/sdk)). As propriedades e os destinos do MSBuild associados a cada SDK dependente são importados. Os destinos de publicação importam o conjunto apropriado de destinos com base no método de publicação usado.

Quando o MSBuild ou o Visual Studio carrega um projeto, as seguintes ações de nível alto ocorrem:

* Compilar projeto
* Computar arquivos a publicar
* Publicar arquivos para o destino

## <a name="compute-project-items"></a>Itens de projeto de computação

Quando o projeto é carregado, os [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (arquivos) são computados. O tipo de item determina como o arquivo é processado. Por padrão, os arquivos *.cs* são incluídos na lista de itens `Compile`. Os arquivos na lista de itens `Compile` são compilados.

A lista de itens `Content` contém arquivos que são publicados juntamente com as saídas de build. Por padrão, os arquivos que correspondem aos padrões `wwwroot\**`, `**\*.config` e `**\*.json` são incluídos na lista de itens `Content`. Por exemplo, o [padrão de recurso de curinga](https://gruntjs.com/configuring-tasks#globbing-patterns) `wwwroot\**` corresponde a todos os arquivos na pasta *wwwroot* e suas subpastas.

::: moniker range=">= aspnetcore-3.0"

O SDK da Web importa o [SDK do Razor](xref:razor-pages/sdk). Como resultado, os arquivos que correspondem aos padrões `**\*.cshtml` e `**\*.razor` também são incluídos na lista de itens `Content`.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

O SDK da Web importa o [SDK do Razor](xref:razor-pages/sdk). Como resultado, os arquivos que correspondem ao padrão `**\*.cshtml` também são incluídos na lista de itens `Content`.

::: moniker-end

Para adicionar explicitamente um arquivo à lista de publicação, adicione o arquivo diretamente no arquivo *.csproj*, conforme mostrado na seção [Incluir Arquivos](#include-files).

Ao selecionar o botão **Publicar** no Visual Studio ou ao publicar da linha de comando:

* Os itens/propriedades são calculados (os arquivos necessários para compilar).
* **Somente Visual Studio**: pacotes NuGet são restaurados. (A restauração precisa ser explícita pelo usuário na CLI.)
* O projeto é compilado.
* Os itens de publicação são computados (os arquivos necessários para a publicação).
* O projeto é publicado (os arquivos computados são copiados para o destino de publicação).

Quando um projeto do ASP.NET Core faz referência a `Microsoft.NET.Sdk.Web` no arquivo de projeto, um arquivo *app_offline.htm* é colocado na raiz do diretório do aplicativo Web. Quando o arquivo estiver presente, o módulo do ASP.NET Core apenas desligará o aplicativo e servirá o arquivo *app_offline.htm* durante a implantação. Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Publicação de linha de comando básica

A publicação de linha de comando funciona em todas as plataformas compatíveis com o .NET Core e não requer o Visual Studio. Nos exemplos a seguir, o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core é executado no diretório do projeto (que contém o arquivo *.csproj*). Se a pasta do projeto não for o diretório de trabalho atual, passe explicitamente no caminho do arquivo de projeto. Por exemplo:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Execute os comandos a seguir para criar e publicar um aplicativo Web:

```dotnetcli
dotnet new mvc
dotnet publish
```

O comando `dotnet publish` produz uma variação da saída a seguir:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

O formato da pasta de publicação padrão é *bin\Debug\\{MONIKER DA ESTRUTURA DE DESTINO}\publish\\* . Por exemplo, *bin\Debug\netcoreapp2.2\publish\\* .

O comando a seguir especifica um build de `Release` e o diretório de publicação:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

O comando `dotnet publish` chama MSBuild, que invoca o destino `Publish`. Quaisquer parâmetros passados para `dotnet publish` são passados para o MSBuild. Os parâmetros `-c` e `-o` mapeiam as propriedades `Configuration` e `OutputPath` do MSBuild, respectivamente.

Propriedades do MSBuild podem ser passadas usando qualquer um dos seguintes formatos:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Por exemplo, o comando a seguir publica um build de `Release` para um compartilhamento de rede. O compartilhamento de rede é especificado com barras "/" ( *//r8/* ) e funciona em todas as plataformas com suporte do .NET Core.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Confirme que o aplicativo publicado para implantação não está em execução. Os arquivos da pasta *publish* são bloqueados quando o aplicativo está em execução. A implantação não pode ocorrer porque arquivos bloqueados não podem ser copiados.

## <a name="publish-profiles"></a>Perfis de publicação

Esta seção usa o Visual Studio 2019 ou posterior para criar um perfil de publicação. Uma vez criado, é possível publicar do Visual Studio ou da linha de comando. Perfis de publicação podem simplificar o processo de publicação e podem existir em qualquer número.

Crie um perfil de publicação no Visual Studio escolhendo um dos seguintes caminhos:

* Clique com o botão direito do mouse no projeto no **Gerenciador de soluções** e selecione **publicar**.
* Selecione **Publicar {NOME DO PROJETO}** no menu **Build**.

A guia **Publicar** da página de capacidades do aplicativo é exibida. Se o projeto não tiver um perfil de publicação, a página **Escolher um destino de publicação** é exibida. Você deverá selecionar um dos seguintes destinos de publicação:

* Serviço de Aplicativo do Azure
* Serviço de Aplicativo do Azure no Linux
* Máquinas Virtuais do Azure
* Pasta
* Implantação da Web, IIS, FTP (para qualquer servidor web)
* Importar Perfil

Para determinar o destino de publicação mais apropriado, confira [Quais opções de publicação são adequadas para mim](/visualstudio/ide/not-in-toc/web-publish-options).

Quando o destino de publicação **Pasta** é selecionado, especifique um caminho de pasta para armazenar os ativos publicados. O caminho de pasta padrão é *bin\\{CONFIGURAÇÃO DO PROJETO}\\{MONIKER DA ESTRUTURA DE DESTINO}\publish\\* . Por exemplo, *bin\Release\netcoreapp2.2\publish\\* . Selecione o botão **Criar Perfil** para concluir.

Depois de um perfil de publicação ser criado, o conteúdo da guia **Publicar** é alterado. O perfil recém-criado é exibido em uma lista suspensa. Abaixo da lista suspensa, selecione **Criar novo perfil** para criar outro novo perfil.

A ferramenta de publicação do Visual Studio cria um arquivo MSBuild *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml* que descreve o perfil de publicação. O arquivo *.pubxml*:

* Contém as definições de configuração de publicação e é consumido pelo processo de publicação.
* Pode ser modificado para personalizar o processo de compilação e publicação.

Ao publicar em um destino do Azure, o arquivo *.pubxml* contém o identificador de assinatura do Azure. Com esse tipo de destino, não é recomendável adicionar esse arquivo ao controle do código-fonte. Ao publicar em um destino não Azure, é seguro fazer check-in do arquivo *.pubxml*.

Informações confidenciais (como a senha de publicação) são criptografadas em um nível por usuário/computador. Elas são armazenadas no arquivo *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml.user*. Já que esse arquivo pode armazenar informações confidenciais, o check-in dele não deve ser realizado no controle do código-fonte.

Para obter uma visão geral de como publicar um aplicativo web ASP.NET Core, confira <xref:host-and-deploy/index>. As tarefas e os destinos do MSBuild necessários para publicar um aplicativo Web ASP.NET Core são de código-fonte aberto no [repositório ASPNET/WebSDK](https://github.com/aspnet/websdk).

Os comandos a seguir podem usar os perfis de publicação Folder, MSDeploy e [kudu](https://github.com/projectkudu/kudu/wiki) . Porque o MSDeploy não oferece suporte em plataforma cruzada, as seguintes opções de MSDeploy têm suporte apenas no Windows.

**Pasta (funciona em plataforma cruzada):**

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**Pacote do MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

Nos exemplos anteriores:

* `dotnet publish` e `dotnet build` dão suporte a APIs kudu para publicar no Azure de qualquer plataforma. A publicação do Visual Studio dá suporte às APIs do Kudu, mas ela é compatível com o WebSDK para publicação multiplataforma para o Azure.
* Não passe `DeployOnBuild` para o comando `dotnet publish`.

Para obter mais informações, confira [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).

Adicione um perfil de publicação à pasta *Properties/PublishProfiles* do projeto com o seguinte conteúdo:

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

## <a name="folder-publish-example"></a>Exemplo de publicação de pasta

Ao publicar com um perfil chamado *FolderProfile*, use um dos seguintes comandos:

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

O comando [dotnet build](/dotnet/core/tools/dotnet-build) da CLI do .NET Core chama `msbuild` para executar a compilação e o processo de publicação. Os comandos `dotnet build` e `msbuild` são equivalentes ao passar em um perfil de pasta. Ao chamar o `msbuild` diretamente no Windows, a versão do .NET Framework do MSBuild é usada. Chamar `dotnet build` em um perfil não de pasta:

* Invoca `msbuild`, que usa MSDeploy.
* Resulta em uma falha (mesmo quando em execução no Windows). Para publicar com um perfil não de pasta, chame o `msbuild` diretamente.

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

No exemplo anterior:

* A propriedade `<ExcludeApp_Data>` está presente, simplesmente para satisfazer um requisito de esquema XML. A propriedade `<ExcludeApp_Data>` não tem efeito sobre o processo de publicação, mesmo se houver uma pasta *App_Data* na raiz do projeto. A pasta *App_Data* não recebe tratamento especial, como faz em projetos do ASP.NET 4.x.

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* A propriedade `<LastUsedBuildConfiguration>` é definida como `Release`. Ao publicar no Visual Studio, o valor de `<LastUsedBuildConfiguration>` é definido usando o valor quando o processo de publicação é iniciado. `<LastUsedBuildConfiguration>` é especial e não deve ser substituída em um arquivo do MSBuild importado. Essa propriedade pode, no entanto, ser substituída na linha de comando usando uma das abordagens a seguir.
  * Usando a CLI do .NET Core:

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * Usando o MSBuild:

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Para saber mais, confira [MSBuild: como definir a propriedade de configuração](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

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
  * **Gerenciador de soluções**: selecione **Exibir**  > **Cloud Explorer**. Conecte-se à sua assinatura do Azure. Abra **Serviços de Aplicativos**. Clique com o botão direito do mouse no aplicativo. Selecione **Baixar Perfil de Publicação**.
  * Portal do Azure: selecione **obter perfil de publicação** no painel **visão geral** do aplicativo Web.

O exemplo a seguir usa um perfil de publicação denominado *AzureWebApp – Implantação da Web*:

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Um perfil de publicação também pode ser usado com o comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) da CLI do .NET Core em um shell de comando do Windows:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> O comando `dotnet msbuild` é um comando de plataforma cruzada e pode compilar os aplicativos ASP.NET Core no macOS e Linux. No entanto, o MSBuild no macOS e Linux não é capaz de implantar um aplicativo no Azure ou em outros pontos de extremidade do MSDeploy.

## <a name="set-the-environment"></a>Definir o ambiente

Inclua a propriedade `<EnvironmentName>` no perfil de publicação ( *.pubxml*) ou no arquivo de projeto para definir o [ambiente](xref:fundamentals/environments) do aplicativo:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Se você precisar de transformações do *web.config* (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), confira <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Excluir arquivos

Ao publicar aplicativos Web ASP.NET Core, os seguintes ativos são incluídos:

* Artefatos de compilação
* As pastas e os arquivos que correspondem aos seguintes padrões de recurso de curinga:
  * `**\*.config` (por exemplo, *web.config*)
  * `**\*.json` (por exemplo, *appsettings.json*)
  * `wwwroot\**`

O MSBuild dá suporte aos [padrões de recurso de curinga](https://gruntjs.com/configuring-tasks#globbing-patterns). Por exemplo, o elemento `<Content>` a seguir suprime a cópia dos arquivos de texto ( *.txt*) na pasta *wwwroot\content* e suas subpastas:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

A marcação anterior pode ser adicionada a um perfil de publicação ou ao arquivo *.csproj*. Quando adicionada ao arquivo *.csproj*, a regra será adicionada a todos os perfis de publicação no projeto.

O elemento `<MsDeploySkipRules>` a seguir exclui todos os arquivos da pasta *wwwroot\content*:

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

O uso da implantação de linha de comando com o elemento `<Content>` anterior produz uma variação da seguinte saída:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
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

As seções a seguir descrevem diferentes abordagens para inclusão de arquivos no momento da publicação. A seção [Inclusão de arquivos gerais](#general-file-inclusion) usa o item `DotNetPublishFiles`, que é fornecido por um arquivo de destinos de publicação no SDK da Web. A seção [Inclusão de arquivos seletivos](#selective-file-inclusion) usa o item `ResolvedFileToPublish`, que é fornecido por um arquivo de destinos de publicação no SDK do .NET Core. Como o SDK para Web depende do SDK do .NET Core, qualquer item pode ser usado em um projeto do ASP.NET Core.

### <a name="general-file-inclusion"></a>Inclusão de arquivos gerais

O elemento `<ItemGroup>` do exemplo a seguir demonstra como copiar uma pasta localizada fora do diretório do projeto para uma pasta do site publicado. Qualquer arquivo adicionado ao `<ItemGroup>` da marcação a seguir são incluídos por padrão.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

A marcação anterior:

* Pode ser adicionada ao arquivo *.csproj* ou ao perfil de publicação. Se ela for adicionada ao arquivo *.csproj*, ela será incluída em cada perfil de publicação no projeto.
* Declara um item `_CustomFiles` para armazenar os arquivos correspondendo ao padrão de recurso de curinga do atributo `Include`. A pasta *imagens* referenciada no padrão está localizada fora do diretório do projeto. Uma [propriedade reservada](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), denominada `$(MSBuildProjectDirectory)`, resolve o caminho absoluto do arquivo de projeto.
* Fornece uma lista de arquivos para o item `DotNetPublishFiles`. Por padrão, o elemento `<DestinationRelativePath>` do item está vazio. O valor padrão é substituído na marcação e usa [metadados de item bem conhecidos](/visualstudio/msbuild/msbuild-well-known-item-metadata), como `%(RecursiveDir)`. O texto interno representa a pasta *wwwroot/images* do site publicado.

### <a name="selective-file-inclusion"></a>Inclusão de arquivo seletivo

A marcação realçada no exemplo a seguir demonstra:

* Copiar um arquivo localizado fora do projeto para a pasta *wwwroot* do site publicado. O nome de arquivo *ReadMe2.md* é mantido.
* Excluir a pasta *wwwroot\Content*.
* Excluir *Views\Home\About2.cshtml*.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

O exemplo anterior usa o item `ResolvedFileToPublish`, cujo comportamento padrão é sempre copiar os arquivos fornecidos no atributo `Include` para o site publicado. Substituir o comportamento padrão, incluindo um elemento filho `<CopyToPublishDirectory>` com o texto interno de um `Never` ou `PreserveNewest`. Por exemplo:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Para obter mais amostras de implantação, confira o [Leiame do repositório do SDK da Web](https://github.com/aspnet/websdk).

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
* [Repositório GitHub do SDK da Web](https://github.com/aspnet/websdk/issues): problemas de arquivo e recursos de solicitação para implantação.
* [Publicar um aplicativo Web ASP.NET em uma VM do Azure usando o Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
