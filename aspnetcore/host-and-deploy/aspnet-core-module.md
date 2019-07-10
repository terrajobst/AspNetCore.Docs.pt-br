---
title: Módulo do ASP.NET Core
author: guardrex
description: Saiba como configurar o módulo do ASP.NET Core para hospedar aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 4a360023cc7fab2f066d490f7f368fc35815703a
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500445"
---
# <a name="aspnet-core-module"></a>Módulo do ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik) e [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-2.2"

O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:

* Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).
* Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).

Versões do Windows compatíveis:

* Windows 7 ou posterior
* Windows Server 2008 R2 ou posterior

Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).

Ao hospedar de fora do processo, o módulo só funciona com o Kestrel. O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modelos de hospedagem

### <a name="in-process-hosting-model"></a>Modelo de hospedagem em processo

Para configurar um aplicativo para hospedagem em processo, adicione a propriedade `<AspNetCoreHostingModel>` ao arquivo de projeto do aplicativo com um valor de `InProcess` (a hospedagem de fora do processo é definida com `OutOfProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.

Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.

As seguintes características se aplicam ao hospedar em processo:

* O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel). Em processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:

  * Registrar o `IISHttpServer`.
  * Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.
  * Configurar o host para capturar erros de inicialização.

* O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.

* Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos. Use um pool de aplicativos por aplicativo.

* Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta. Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.

* A arquitetura (número de bit) do aplicativo e o tempo de execução instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.

* Se a configuração manual do host do aplicativo com `WebHostBuilder` (não usando [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) e o aplicativo não forem executados diretamente no servidor Kestrel (auto-hospedado), chame `UseKestrel` antes de chamar `UseIISIntegration`. Se a ordem for invertida, a inicialização do host falhará.

* As desconexões do cliente são detectadas. O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.

* No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).

  Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Chame o método `SetCurrentDirectory`. As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.

* Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário. Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão. Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a>Modelo de hospedagem de fora do processo

Para configurar um aplicativo para hospedagem fora do processo, use qualquer uma das abordagens a seguir no arquivo de projeto:

* não especifique a propriedade `<AspNetCoreHostingModel>`. Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.
* Defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` (a hospedagem no processo é definida com `InProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).

Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:

* Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.
* Configurar o host para capturar erros de inicialização.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Ao hospedar fora do processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário. Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão. Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
    services.AddAuthentication(IISDefaults.AuthenticationScheme);
}

public void Configure(IApplicationBuilder app)
{
    app.UseAuthentication();
}
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="hosting-model-changes"></a>Alterações no modelo de hospedagem

Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.

Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express. A próxima solicitação para o aplicativo gera um novo processo do IIS Express.

### <a name="process-name"></a>Nome do processo

`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.

Versões do Windows compatíveis:

* Windows 7 ou posterior
* Windows Server 2008 R2 ou posterior

O módulo só funciona com o Kestrel. O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).

Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos. O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha. Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

As solicitações chegam da Web para o driver do HTTP.sys no modo kernel. O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS). O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.

O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor para escutar em `http://localhost:{port}`. Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas. O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.

Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo. O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel. A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.

::: moniker-end

Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos. Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.

O Módulo do ASP.NET Core também pode:

* Definir variáveis de ambiente para o processo de trabalho.
* Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.
* Encaminhar tokens de autenticação do Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Como instalar e usar o Módulo do ASP.NET Core

Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Configuração com web.config

O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.

O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`. O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.

Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributos do elemento aspNetCore

::: moniker range=">= aspnetcore-2.2"

| Atributo | DESCRIÇÃO | Padrão |
| --------- | ----------- | :-----: |
| `arguments` | <p>Atributo de cadeia de caracteres opcional.</p><p>Argumentos para o executável especificado em **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Atributo booliano opcional.</p><p>Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Atributo booliano opcional.</p><p>Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação. É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</p> | `true` |
| `hostingModel` | <p>Atributo de cadeia de caracteres opcional.</p><p>Especifica o modelo de hospedagem como em processo (`InProcess`) ou de fora do processo (`OutOfProcess`).</p> | `OutOfProcess` |
| `processesPerApplication` | <p>Atributo inteiro opcional.</p><p>Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</p><p>&dagger;Para hospedagem em processo, o valor está limitado a `1`.</p><p>A configuração `processesPerApplication` é desencorajada. Esse atributo será removido em uma versão futura.</p> | Padrão: `1`<br>Mín.: `1`<br>Máx.: `100`&dagger; |
| `processPath` | <p>Atributo de cadeia de caracteres obrigatório.</p><p>Caminho para o executável que inicia um processo que escuta solicitações HTTP. Caminhos relativos são compatíveis. Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</p> | |
| `rapidFailsPerMinute` | <p>Atributo inteiro opcional.</p><p>Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto. Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</p><p>Sem suporte com hospedagem padrão.</p> | Padrão: `10`<br>Mín.: `0`<br>Máx.: `100` |
| `requestTimeout` | <p>Atributo de intervalo de tempo opcional.</p><p>Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</p><p>Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</p><p>Não se aplica à hospedagem em processo. Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</p><p>Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59. O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</p> | Padrão: `00:02:00`<br>Mín.: `00:00:00`<br>Máx.: `360:00:00` |
| `shutdownTimeLimit` | <p>Atributo inteiro opcional.</p><p>Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</p> | Padrão: `10`<br>Mín.: `0`<br>Máx.: `600` |
| `startupTimeLimit` | <p>Atributo inteiro opcional.</p><p>Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta. Se esse tempo limite é excedido, o módulo encerra o processo. O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</p><p>Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</p> | Padrão: `120`<br>Mín.: `0`<br>Máx.: `3600` |
| `stdoutLogEnabled` | <p>Atributo booliano opcional.</p><p>Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Atributo de cadeia de caracteres opcional.</p><p>Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log. Os caminhos relativos são relativos à raiz do site. Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos. Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado. Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**. Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| Atributo | DESCRIÇÃO | Padrão |
| --------- | ----------- | :-----: |
| `arguments` | <p>Atributo de cadeia de caracteres opcional.</p><p>Argumentos para o executável especificado em **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Atributo booliano opcional.</p><p>Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Atributo booliano opcional.</p><p>Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação. É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</p> | `true` |
| `processesPerApplication` | <p>Atributo inteiro opcional.</p><p>Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</p><p>A configuração `processesPerApplication` é desencorajada. Esse atributo será removido em uma versão futura.</p> | Padrão: `1`<br>Mín.: `1`<br>Máx.: `100` |
| `processPath` | <p>Atributo de cadeia de caracteres obrigatório.</p><p>Caminho para o executável que inicia um processo que escuta solicitações HTTP. Caminhos relativos são compatíveis. Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</p> | |
| `rapidFailsPerMinute` | <p>Atributo inteiro opcional.</p><p>Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto. Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</p> | Padrão: `10`<br>Mín.: `0`<br>Máx.: `100` |
| `requestTimeout` | <p>Atributo de intervalo de tempo opcional.</p><p>Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</p><p>Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</p> | Padrão: `00:02:00`<br>Mín.: `00:00:00`<br>Máx.: `360:00:00` |
| `shutdownTimeLimit` | <p>Atributo inteiro opcional.</p><p>Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</p> | Padrão: `10`<br>Mín.: `0`<br>Máx.: `600` |
| `startupTimeLimit` | <p>Atributo inteiro opcional.</p><p>Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta. Se esse tempo limite é excedido, o módulo encerra o processo. O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</p><p>Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</p> | Padrão: `120`<br>Mín.: `0`<br>Máx.: `3600` |
| `stdoutLogEnabled` | <p>Atributo booliano opcional.</p><p>Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Atributo de cadeia de caracteres opcional.</p><p>Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log. Os caminhos relativos são relativos à raiz do site. Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos. As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log. Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**. Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a>Definindo variáveis de ambiente

::: moniker range=">= aspnetcore-3.0"

Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`. Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`. Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`. Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.

> [!WARNING]
> As variáveis de ambiente definidas nesta seção são conflitantes com as variáveis de ambiente do sistema definidas com o mesmo nome. Quando a variável de ambiente é definida no arquivo *web.config* e no nível do sistema do Windows, o valor do arquivo *web.config* fica anexado ao valor da variável de ambiente do sistema (por exemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), o que impede a inicialização do aplicativo.

::: moniker-end

O exemplo a seguir define duas variáveis de ambiente. `ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`. Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo. `CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> Em vez de configurar o ambiente diretamente no *web.config*, você pode incluir a propriedade `<EnvironmentName>` no perfil de publicação ( *.pubxml*) ou no perfil de projeto. Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.

## <a name="appofflinehtm"></a>app_offline.htm

Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada. Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.

Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*. Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.

::: moniker range=">= aspnetcore-2.2"

Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta. Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.

::: moniker-end

## <a name="start-up-error-page"></a>Página de erro de inicialização

::: moniker range=">= aspnetcore-2.2"

A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.

Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.

No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.

Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.

Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`. Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida. Para omitir esta página e reverter para a página de código de status 502 padrão do IIS, use o atributo `disableStartUpErrorPage`. Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

![Página de código de status 502.5 – Falha do Processo](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a>Criação de log e redirecionamento

O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos. Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado. O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).

Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo. É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.

Usar o log de stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo. Não use o log de stdout para fins gerais de registro em log do aplicativo. Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado. O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo ( *.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados. Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.

::: moniker range=">= aspnetcore-2.2"

Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB. Após a inicialização, todos os logs adicionais são descartados.

::: moniker-end

O elemento `aspNetCore` de exemplo a seguir configura o registro em log de stdout para um aplicativo hospedado no Serviço de Aplicativo do Azure. Um caminho local ou um caminho de compartilhamento de rede é aceitável para o registro em log local. Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a>Logs de diagnóstico avançados

O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados. Adicione o elemento `<handlerSettings>` ao elemento `<aspNetCore>` no *web.config*. A definição de `debugLevel` como `TRACE` expõe uma fidelidade maior de informações de diagnóstico:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Todas as pastas no caminho (*logs* no exemplo anterior) são criadas pelo módulo quando o arquivo de log é criado. O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

As pastas no caminho fornecido para o valor `<handlerSetting>` (*logs* no exemplo anterior) não são criadas automaticamente pelo módulo e devem existir previamente na implantação. O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.

Níveis (na ordem do menos para o mais detalhado):

* ERROR
* WARNING
* INFO
* TRACE

Locais (vários locais são permitidos):

* CONSOLE
* EVENTLOG
* FILE

As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:

* `ASPNETCORE_MODULE_DEBUG_FILE` &ndash; caminho para o arquivo de log de depuração. (Padrão: *aspnetcore-debug.log*)
* `ASPNETCORE_MODULE_DEBUG` &ndash; configuração do nível de depuração.

> [!WARNING]
> **Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema. O tamanho do log não é limitado. Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.

::: moniker-end

Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.

::: moniker range=">= aspnetcore-3.0"

## <a name="modify-the-stack-size"></a>Modificar o tamanho da pilha

Configurar o tamanho da pilha gerenciada usando a configuração `stackSize` em bytes. O tamanho padrão é de `1048576` bytes (1 MB).

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

::: moniker-end

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>A configuração de proxy usa o protocolo HTTP e um token de emparelhamento

::: moniker range=">= aspnetcore-2.2"

*Só se aplica à hospedagem de fora do processo.*

::: moniker-end

O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP. O uso de HTTP é uma otimização de desempenho na qual o tráfego entre o módulo e o Kestrel ocorre em um endereço de loopback fora do adaptador de rede. Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.

Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem. O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo. O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy. O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente. Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada. A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor. Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Módulo do ASP.NET Core com uma configuração do IIS compartilhada

O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**. Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.

::: moniker range=">= aspnetcore-2.2"

Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:

1. Desabilite a configuração compartilhada de IIS.
1. Execute o instalador.
1. Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.
1. Reabilite a Configuração Compartilhada do IIS.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ao usar uma configuração compartilhada de IIS, siga estas etapas:

1. Desabilite a configuração compartilhada de IIS.
1. Execute o instalador.
1. Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.
1. Reabilite a Configuração Compartilhada do IIS.

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Versão do módulo e logs do instalador do pacote de hospedagem

Para determinar a versão do Módulo do ASP.NET Core instalado:

1. No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.
1. Localize o arquivo *aspnetcore.dll*.
1. Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.
1. Selecione a guia **Detalhes**. A **Versão do arquivo** e a **Versão do produto** representam a versão instalada do módulo.

Os logs de instalador do pacote de hospedagem para o módulo são encontrados em *C:\\Usuários\\%UserName%\\AppData\\Local\\Temp*. O arquivo é nomeado *dd_DotNetCoreWinSvrHosting__\<carimbo de data/hora>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Locais dos arquivos de módulo, de esquema e de configuração

### <a name="module"></a>Módulo

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

::: moniker range=">= aspnetcore-2.2"

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

::: moniker-end

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

::: moniker range=">= aspnetcore-2.2"

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

::: moniker-end

### <a name="schema"></a>Esquema

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

::: moniker range=">= aspnetcore-2.2"

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

::: moniker-end

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

::: moniker range=">= aspnetcore-2.2"

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

::: moniker-end

### <a name="configuration"></a>Configuração

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:host-and-deploy/iis/index>
* [Repositório do GitHub do Módulo do ASP.NET Core (origem de referência)](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
