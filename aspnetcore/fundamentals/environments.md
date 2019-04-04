---
title: Usar vários ambientes no ASP.NET Core
author: rick-anderson
description: Aprenda a controlar o comportamento do aplicativo em vários ambientes em aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/environments
ms.openlocfilehash: 4fc43935aa058efc4497d3d9eb607df6c0899443
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809231"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Usar vários ambientes no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de tempo de execução usando uma variável de ambiente.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ambientes

O ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname). Você pode definir `ASPNETCORE_ENVIRONMENT` como qualquer valor, mas há suporte para [três valores](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) na estrutura: [Desenvolvimento](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Preparo](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) e [Produção](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Se `ASPNETCORE_ENVIRONMENT` não estiver definido, o padrão será `Production`.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

O código anterior:

* Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.
* Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:

  * `Staging`
  * `Production`
  * `Staging_2`

O [Auxiliar de Marcação de Ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa o valor de `IHostingEnvironment.EnvironmentName` para incluir ou excluir a marcação no elemento:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

No Windows e no macOS, valores e variáveis de ambiente não diferenciam maiúsculas de minúsculas. Valores e variáveis de ambiente do Linux **diferenciam maiúsculas de minúsculas** por padrão.

### <a name="development"></a>Desenvolvimento

O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção. Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.

O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto. Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.

O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores. Use um ponto e vírgula entre as URLs na lista:
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado. O valor de `commandName` especifica o servidor Web a ser iniciado. `commandName` pode ser qualquer um dos seguintes:

* `IISExpress`
* `IIS`
* `Project` (que inicia o Kestrel)

Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):

* *launchSettings.json* é lido, se está disponível. As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.
* O ambiente de hospedagem é exibido.

A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado. O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.

> [!WARNING]
> *launchSettings.json* não deve armazenar segredos. A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.

Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*. O exemplo a seguir define o ambiente como `Development`:

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*. Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.

### <a name="production"></a>Produção

O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo. Algumas configurações comuns que são diferentes do desenvolvimento incluem:

* Cache.
* Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.
* Páginas de erro de diagnóstico desabilitadas.
* Páginas de erro amigáveis habilitadas.
* Log de produção e monitoramento habilitados. Por exemplo, [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Definir o ambiente

Geralmente, é útil definir um ambiente específico para teste. Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração. O método para configurar o ambiente depende do sistema operacional.

### <a name="azure-app-service"></a>Serviço de Aplicativo do Azure

Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:

1. Selecione o aplicativo na folha **Serviços de Aplicativos**.
1. No grupo **CONFIGURAÇÕES**, selecione a folha **Configurações do aplicativo**.
1. Na área **Configurações do aplicativo**, selecione **Adicionar nova configuração**.
1. Para **Inserir um nome**, forneça `ASPNETCORE_ENVIRONMENT`. Para **Inserir um valor**, fornecer o ambiente (por exemplo, `Staging`).
1. Marque a caixa de seleção **Configuração do Slot** se desejar que a configuração do ambiente permaneça no slot atual quando os slots de implantação forem trocados. Para obter mais informações, confira [Documentação do Azure: que configurações são trocadas?](/azure/app-service/web-sites-staged-publishing).
1. Selecione **Salvar** na parte superior da folha.

O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.

### <a name="windows"></a>Windows

Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:

**Prompt de comando**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Esses comandos somente têm efeito para a janela atual. Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.

Para definir o valor globalmente no Windows, use uma das seguintes abordagens:

* Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:

  **Prompt de comando**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  O comutador `/M` indica para definir a variável de ambiente no nível do sistema. Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema. Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.

Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.

**web.config**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>. Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida com *web.config*, seu valor substitui uma configuração no nível do sistema.

::: moniker range=">= aspnetcore-2.2"

**Arquivo de projeto ou perfil de publicação**

**Para implantações do Windows IIS:** Inclua a propriedade `<EnvironmentName>` no perfil de publicação (*.pubxml*) ou no arquivo de projeto. Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

**Por pool de aplicativos do IIS**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.

> [!IMPORTANT]
> Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:
>
> * Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.
> * Reinicie o servidor.

### <a name="macos"></a>macOS

A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*. Edite o arquivo usando qualquer editor de texto. Adicione a seguinte instrução:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a>Linux

Para distribuições Linux, use o comando `export` no prompt de comando para as configurações de variável baseadas na sessão e o arquivo *bash_profile* para as configurações de ambiente no nível do computador.

### <a name="configuration-by-environment"></a>Configuração por ambiente

Para carregar a configuração por ambiente, recomendamos:

* Arquivos *appsettings* (*appsettings.\<Environment>.json*). Confira [Configuração: provedor de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).
* Variáveis de ambiente (definidas em cada sistema em que o aplicativo está hospedado). Confira [Configuração: provedor de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider) e [Armazenamento seguro de segredos do aplicativo em desenvolvimento: variáveis de ambiente](xref:security/app-secrets#environment-variables).
* Gerenciador de Segredo (somente no ambiente de desenvolvimento). Consulte <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Métodos e classe Startup baseados no ambiente

### <a name="startup-class-conventions"></a>Convenções da classe Startup

Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo. O aplicativo pode definir classes `Startup` separadas para ambientes diferentes (por exemplo, `StartupDevelopment`), e a classe `Startup` adequada é selecionada em tempo de execução. A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada. Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada.

Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}
```

Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>Convenções do método Startup

[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) são compatíveis com versões específicas do ambiente dos formatos `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services`:

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
