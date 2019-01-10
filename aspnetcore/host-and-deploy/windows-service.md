---
title: Hospedar o ASP.NET Core em um serviço Windows
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/01/2018
uid: host-and-deploy/windows-service
ms.openlocfilehash: bdb29c318c66ac884b9225ba8c2a0dfc1f364255
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637697"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hospedar o ASP.NET Core em um serviço Windows

Por [Luke Latham](https://github.com/guardrex) e [Tom Dykstra](https://github.com/tdykstra)

Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS. Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="deployment-type"></a>Tipo de implantação

Você pode criar uma implantação de Serviço Windows dependente de estrutura ou autocontida. Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

### <a name="framework-dependent-deployment"></a>Implantação dependente de estrutura

A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino. Quando o cenário FDD é usado com um aplicativo de Serviço Windows do ASP.NET Core, o SDK produz um executável (*\*.exe*), chamado de *executável dependente de estrutura*.

### <a name="self-contained-deployment"></a>Implantação autocontida

A SCD (Implantação Autocontida) não se baseia na presença de componentes compartilhados no sistema de destino. O tempo de execução e as dependências do aplicativo são implantados com o aplicativo para o sistema de hospedagem.

## <a name="convert-a-project-into-a-windows-service"></a>Converter um projeto em um serviço Windows

Faça as seguintes alterações em um projeto ASP.NET Core existente para executar o aplicativo como um serviço:

### <a name="project-file-updates"></a>Atualizações de arquivo de projeto

Com base na sua escolha de [tipo de implantação](#deployment-type), atualize o arquivo de projeto:

#### <a name="framework-dependent-deployment-fdd"></a>FDD (Implantação dependente de estrutura)

Adicione um [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ao `<PropertyGroup>` que contém a estrutura de destino. Adicione a propriedade `<SelfContained>` definida como `false`. Desabilite a criação de um arquivo *web.config* adicionando a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

::: moniker range=">= aspnetcore-2.2"

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

#### <a name="self-contained-deployment-scd"></a>SCD (Implantação Autossuficiente)

Confirme a presença de um [RID (Identificador de Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ou adicione um RID ao `<PropertyGroup>` que contém a estrutura de destino. Desabilite a criação de um arquivo *web.config* adicionando a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Para publicar para vários RIDs:

* Forneça os RIDs em uma lista delimitada por ponto e vírgula.
* Use o nome da propriedade `<RuntimeIdentifiers>` (plural).

  Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).

Adicionar uma referência de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).

Para habilitar o registro em log do Log de Eventos do Windows, adicione uma referência de pacote para [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).

### <a name="programmain-updates"></a>Atualizações de Program.Main

Faça as seguintes alterações em `Program.Main`:

* Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console. Verifique se o depurador está anexado ou se há um argumento de linha de comado do `--console` presente.

  Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> no Host da Web.

  Se as condições forem falsas (o aplicativo for executado como um serviço):

  * Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo. Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando `GetCurrentDirectory` é chamado. Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).
  * Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.

  Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.

* Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*. Para fins de teste e demonstração, o arquivo de configurações de Produção do aplicativo de exemplo define o nível de log para `Information`. Na produção, o valor normalmente é definido como `Error`. Para obter mais informações, consulte <xref:fundamentals/logging/index#windows-eventlog-provider>.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

### <a name="publish-the-app"></a>Publique o aplicativo

Publique o aplicativo usando [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), um [perfil de publicação do Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles) ou o Visual Studio Code. Ao usar o Visual Studio, selecione o **FolderProfile** e configure o **Local de destino** antes de selecionar o botão **Publicar**.

Para publicar o aplicativo de exemplo usando as ferramentas da CLI (Interface de Linha de Comando), execute o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) em um prompt de comando da pasta do projeto, passando a configuração de uma versão para a opção [-c|--configuration](/dotnet/core/tools/dotnet-publish#options). Use a opção [-o|--output](/dotnet/core/tools/dotnet-publish#options) com um caminho para publicar em uma pasta fora do aplicativo.

#### <a name="publish-a-framework-dependent-deployment-fdd"></a>Publicar uma FDD (Implantação Dependente de Estrutura)

No exemplo a seguir, o aplicativo é publicado na pasta *c:\\svc*:

```console
dotnet publish --configuration Release --output c:\svc
```

#### <a name="publish-a-self-contained-deployment-scd"></a>Publicar uma SCD (Implantação Autossuficiente)

O RID deve ser especificado na propriedade `<RuntimeIdenfifier>` (ou `<RuntimeIdentifiers>`) do arquivo de projeto. Insira o tempo de execução na opção [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) do comando `dotnet publish`.

No exemplo a seguir, o aplicativo é publicado para o tempo de execução `win7-x64` para a pasta *c:\\svc*:

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

### <a name="create-a-user-account"></a>Criar uma conta de usuário

Crie uma conta de usuário para o serviço usando o comando `net user`:

```console
net user {USER ACCOUNT} {PASSWORD} /add
```

Para o aplicativo de exemplo, crie uma conta de usuário com o nome `ServiceUser` e uma senha. No comando a seguir, substitua `{PASSWORD}` por uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).

```console
net user ServiceUser {PASSWORD} /add
```

Se você precisar adicionar o usuário a um grupo, use o comando `net localgroup`, onde `{GROUP}` é o nome do grupo:

```console
net localgroup {GROUP} {USER ACCOUNT} /add
```

Para obter mais informações, confira [Contas de Usuário do Serviço](/windows/desktop/services/service-user-accounts).

### <a name="set-permissions"></a>Configurar permissões

Conceda acesso de gravação/leitura/execução para a pasta do aplicativo usando o comando [icacls](/windows-server/administration/windows-commands/icacls):

```console
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* `{PATH}` &ndash; Caminho para a pasta do aplicativo.
* `{USER ACCOUNT}` &ndash; A conta de usuário (SID).
* `(OI)` &ndash; O sinalizador de Herança de Objeto propaga as permissão para os arquivos subordinados.
* `(CI)` &ndash; O sinalizador de Herança de Contêiner propaga as permissão para as pastas subordinadas.
* `{PERMISSION FLAGS}` &ndash; Define as permissões de acesso do aplicativo.
  * Gravar (`W`)
  * Ler (`R`)
  * Executar (`X`)
  * Completo (`F`)
  * Modificar (`M`)
* `/t` &ndash; Aplique recursivamente aos arquivos e pastas subordinadas existentes.

Para o aplicativo de exemplo publicado na pasta *c:\\svc* e a conta `ServiceUser` com permissões de gravação/leitura/execução, use o seguinte comando:

```console
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

Para obter mais informações, confira [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="manage-the-service"></a>Gerenciar o serviço

### <a name="create-the-service"></a>Criar o serviço

Use a ferramenta de linha de comando [sc.exe](https://technet.microsoft.com/library/bb490995) para criar o serviço. O valor `binPath` é o caminho para o executável do aplicativo, que inclui o nome do arquivo executável. **É necessário o espaço entre o sinal de igual e o caractere de aspas de cada parâmetro e valor.**

```console
sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
```

* `{SERVICE NAME}` &ndash; O nome a ser atribuído ao serviço no [Gerenciador de Controle de Serviço](/windows/desktop/services/service-control-manager).
* `{PATH}` &ndash; O caminho para o executável do serviço.
* `{DOMAIN}` &ndash; O domínio de um computador ingressado no domínio. Se o computador não estiver ingressado no domínio, o nome do computador local.
* `{USER ACCOUNT}` &ndash; A conta do usuário na qual o serviço é executado.
* `{PASSWORD}` &ndash; A senhas da conta de usuário.

> [!WARNING]
> **Não** omita o parâmetro `obj`. O valor padrão para `obj` é a [conta LocalSystem](/windows/desktop/services/localsystem-account). Executar um serviço na conta `LocalSystem` apresenta um risco de segurança significativo. Sempre execute um serviço com uma conta de usuário com privilégios restritos.

Observe o seguinte aplicativo de exemplo:

* O nome do serviço é **MyService**.
* O serviço publicado reside na pasta *c:\\svc*. O executável do aplicativo é chamado de *SampleApp.exe*. Coloque o valor `binPath` entre aspas duplas (").
* O serviço é executado na conta `ServiceUser`. Substitua `{DOMAIN}` com o domínio ou nome do computador local da conta de usuário. Coloque o valor `obj` entre aspas duplas ("). Exemplo: se o sistema de hospedagem é um computador local denominado `MairaPC`, defina `obj` como `"MairaPC\ServiceUser"`.
* Substitua `{PASSWORD}` com a senha da conta do usuário. Coloque o valor `password` entre aspas duplas (").

```console
sc create MyService binPath= "c:\svc\sampleapp.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
```

> [!IMPORTANT]
> Certifique-se de que os espaços entre os sinais de igual e os valores dos parâmetros estão presentes.

### <a name="start-the-service"></a>Iniciar o serviço

Inicie o serviço com o comando `sc start {SERVICE NAME}`.

Para iniciar o serviço de aplicativo de exemplo, use o seguinte comando:

```console
sc start MyService
```

O comando leva alguns segundos para iniciar o serviço.

### <a name="determine-the-service-status"></a>Determinar o status do serviço

Para verificar o status do serviço, use o comando `sc query {SERVICE NAME}`. O status é relatado como um dos seguintes valores:

* `START_PENDING`
* `RUNNING`
* `STOP_PENDING`
* `STOPPED`

Use o seguinte comando para verificar o status do serviço de aplicativo de exemplo:

```console
sc query MyService
```

### <a name="browse-a-web-app-service"></a>Procurar um serviço de aplicativo Web

Quando o serviço estiver no estado `RUNNING` e se o serviço for um aplicativo Web, procure o aplicativo em seu caminho (por padrão, `http://localhost:5000`, que redireciona para `https://localhost:5001` ao usar [Middleware de Redirecionamento HTTPS](xref:security/enforcing-ssl)).

Para o serviço de aplicativo de exemplo, procure o aplicativo em `http://localhost:5000`.

### <a name="stop-the-service"></a>Parar o serviço

Interrompa o serviço com o comando `sc stop {SERVICE NAME}`.

O comando a seguir interrompe o serviço de aplicativo de exemplo:

```console
sc stop MyService
```

### <a name="delete-the-service"></a>Excluir o serviço

Após um pequeno atraso para interromper um serviço, desinstale o serviço com o comando `sc delete {SERVICE NAME}`.

Verifique o status do serviço de aplicativo de exemplo:

```console
sc query MyService
```

Quando o serviço de aplicativo de exemplo estiver no estado `STOPPED`, use o seguinte comando para desinstalar o serviço de aplicativo de exemplo:

```console
sc delete MyService
```

## <a name="handle-starting-and-stopping-events"></a>Manipular eventos de início e de parada

Para manipular os eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, faça as seguintes alterações adicionais:

1. Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Para ver o local do <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, consulte o exemplo de código mostrado na seção [Converter um projeto em um Serviço Windows](#convert-a-project-into-a-windows-service).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional. Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-https"></a>Configurar o HTTPS

Para configurar o serviço com um ponto de extremidade seguro:

1. Crie um certificado X.509 para o sistema de hospedagem usando os mecanismos de implantação e aquisição do certificado da sua plataforma.

1. Especifique uma [Configuração do ponto de extremidade HTTPS do servidor Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) para usar o certificado.

Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.

## <a name="current-directory-and-content-root"></a>Diretório atual e a raiz do conteúdo

O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*. A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações). Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Defina o caminho da raiz do conteúdo para a pasta do aplicativo

O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando o serviço é criado. Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configuração, chame <xref:System.IO.Directory.SetCurrentDirectory*> com o caminho para a raiz do conteúdo do aplicativo.

No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a>Armazene os arquivos do serviço em um local adequado no disco

Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.

## <a name="additional-resources"></a>Recursos adicionais

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
