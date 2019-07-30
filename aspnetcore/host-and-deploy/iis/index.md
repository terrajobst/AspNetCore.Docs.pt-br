---
title: Hospedar o ASP.NET Core no Windows com o IIS
author: guardrex
description: Saiba como hospedar aplicativos ASP.NET Core no Windows Server IIS (Serviços de Informações da Internet).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2019
uid: host-and-deploy/iis/index
ms.openlocfilehash: 7677173493e68f5a5656c18533e0ae13a7c5bece
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602462"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Hospedar o ASP.NET Core no Windows com o IIS

Por [Luke Latham](https://github.com/guardrex)

[Instalar o pacote de hospedagem do .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

Há suporte para os seguintes sistemas operacionais:

* Windows 7 ou posterior
* Windows Server 2008 R2 ou posterior

O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente chamado de WebListener) não funciona em uma configuração de proxy reverso com o IIS. Use o [servidor Kestrel](xref:fundamentals/servers/kestrel).

Para obter mais informações sobre hospedagem no Azure, consulte <xref:host-and-deploy/azure-apps/index>.

Para obter as diretrizes de solução de problemas, consulte <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Plataformas com suporte

Aplicativos publicados para implantação de 32 bits (x86) ou 64 bits (x64) têm suporte. Implantar um aplicativo de 32 bits com um SDK do .NET Core de 32 bits (x86), a menos que o aplicativo:

* Exija o maior espaço de endereço de memória virtual disponível para um aplicativo de 64 bits.
* Exija o maior tamanho de pilha do IIS.
* Tenha dependências nativas de 64 bits.

Use um SDK do .NET Core de 64 bits (x64) para publicar um aplicativo de 64 bits. Um tempo de execução de 64 bits deve estar presente no sistema host.

::: moniker range=">= aspnetcore-2.2"

## <a name="hosting-models"></a>Modelos de hospedagem

### <a name="in-process-hosting-model"></a>Modelo de hospedagem em processo

Usando uma hospedagem em processo, um aplicativo ASP.NET Core é executado no mesmo processo que seu processo de trabalho do IIS. A hospedagem em processo oferece desempenho melhor em hospedagem fora do processo porque as solicitações não são transmitidas por proxy pelo adaptador de loopback, um adaptador de rede que retorna o tráfego de rede de saída para o mesmo computador. O IIS manipula o gerenciamento de processos com o [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

O [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module):

* Executa a inicialização do aplicativo.
  * Carrega o [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Chama `Program.Main`.
* Manipula o tempo de vida da solicitação nativa do IIS.

Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado em processo:

![Módulo do ASP.NET Core no cenário de hospedagem em processo](index/_static/ancm-inprocess.png)

A solicitação chega da Web para o driver do HTTP.sys no modo kernel. O driver roteia as solicitações nativas ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS). O módulo recebe a solicitação nativa e a passa para o Servidor HTTP do IIS (`IISHttpServer`). O servidor HTTP do IIS é uma implementação de servidor em processo do IIS que converte a solicitação de nativa para gerenciada.

Depois que o Servidor HTTP do IIS processa a solicitação, a solicitação é enviada por push para o pipeline de middleware do ASP.NET Core. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo. A resposta do aplicativo é retornada ao IIS por meio do Servidor HTTP do IIS. O IIS enviará a resposta ao cliente que iniciou a solicitação.

A hospedagem em processo é uma opção de aceitação para os aplicativos existentes, mas o padrão dos modelos [dotnet new](/dotnet/core/tools/dotnet-new) é o modelo de hospedagem em processo para todos os cenários do IIS e do IIS Express.

`CreateDefaultBuilder` adiciona uma instância <xref:Microsoft.AspNetCore.Hosting.Server.IServer> chamando o método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para inicializar o [CoreCLR](/dotnet/standard/glossary#coreclr) e hospedar o aplicativo no processo de trabalho do IIS (*w3wp.exe* ou *iisexpress.exe*). Os testes de desempenho indicam que hospedar um aplicativo em processo do .NET Core oferece uma taxa de transferência de solicitação significativamente mais elevada em comparação a hospedar o aplicativo fora do processo e enviar por proxy as solicitações para o servidor [Kestrel](xref:fundamentals/servers/kestrel).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

> [!NOTE]
> Aplicativos publicados como um único arquivo executável não podem ser carregados pelo modelo de hospedagem em processo.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="out-of-process-hosting-model"></a>Modelo de hospedagem de fora do processo

Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos. O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha. Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:

![Módulo do ASP.NET Core no cenário de hospedagem fora do processo](index/_static/ancm-outofprocess.png)

As solicitações chegam da Web para o driver do HTTP.sys no modo kernel. O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS). O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.

O módulo especifica a porta por meio de uma variável de ambiente na inicialização e a extensão <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> configura o servidor para escutar em `http://localhost:{PORT}`. Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas. O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.

Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo. O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel. A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), um servidor HTTP padrão multiplataforma.

Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado em um processo separado do processo de trabalho do IIS (*fora do processo*) com o [servidor Kestrel](xref:fundamentals/servers/index#kestrel).

Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos. O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha. Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:

![Módulo do ASP.NET Core](index/_static/ancm-outofprocess.png)

As solicitações chegam da Web para o driver do HTTP.sys no modo kernel. O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS). O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.

O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor para escutar em `http://localhost:{port}`. Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas. O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.

Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo. O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel. A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.

`CreateDefaultBuilder` configura o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e habilita a integração do IIS, configurando o caminho base e a porta para o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

O Módulo do ASP.NET Core gera uma porta dinâmica a ser atribuída ao processo de back-end. `CreateDefaultBuilder` chama o método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>. `UseIISIntegration` configura o Kestrel para escutar na porta dinâmica no endereço IP do localhost (`127.0.0.1`). Se a porta dinâmica for 1234, o Kestrel escutará em `127.0.0.1:1234`. Essa configuração substitui outras configurações de URL fornecidas por:

* `UseUrls`
* [API de escuta do Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Configuração](xref:fundamentals/configuration/index) (ou [opção --urls de linha de comando](xref:fundamentals/host/web-host#override-configuration))

As chamadas a `UseUrls` ou à API `Listen` do Kestrel não são necessárias ao usar o módulo. Se `UseUrls` ou `Listen` for chamado, o Kestrel escutará na porta especificada somente durante a execução do aplicativo sem o IIS.

Para obter mais informações sobre os modelos de hospedagem em processo e fora dele, veja [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) (Módulo do ASP.NET Core) e [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module) (Referência de configuração do módulo do ASP.NET Core).

::: moniker-end

Para orientação sobre a configuração do Módulo do ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.

Para saber mais sobre hospedagem, confira [Host no ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configuração do aplicativo

### <a name="enable-the-iisintegration-components"></a>Habilitar os componentes de IISIntegration

Um *Program.cs* típico chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> para começar a configurar um host que permite a integração com o IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

### <a name="iis-options"></a>Opções do IIS

::: moniker range=">= aspnetcore-2.2"

**Modelo de hospedagem em processo**

Para configurar as opções do Servidor IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISServerOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. O exemplo a seguir desabilita AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

| Opção                         | Padrão | Configuração |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Se `true`, o Servidor do IIS define o `HttpContext.User` autenticado pela [Autenticação do Windows](xref:security/authentication/windowsauth). Se `false`, o servidor fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`. A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione. Para obter mais informações, veja [Autenticação do Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Configura o nome de exibição mostrado aos usuários em páginas de logon. |
| `AllowSynchronousIO`           | `false` | Se a E/S síncrona é permitida para `HttpContext.Request` e `HttpContext.Response`. |
| `MaxRequestBodySize`           | `30000000`  | Obtém ou define o tamanho máximo do corpo da solicitação para o `HttpRequest`. Observe que o próprio IIS tem o limite `maxAllowedContentLength` que será processado antes de `MaxRequestBodySize` definido no `IISServerOptions`. Alterar `MaxRequestBodySize` não afetará `maxAllowedContentLength`. Para aumentar `maxAllowedContentLength`, adicione uma entrada em *web.config* para definir `maxAllowedContentLength` para um valor mais alto. Para obter mais detalhes, confira [Configuração](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

**Modelo de hospedagem de fora do processo**

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Opção                         | Padrão | Configuração |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Se `true`, o Servidor do IIS define o `HttpContext.User` autenticado pela [Autenticação do Windows](xref:security/authentication/windowsauth). Se `false`, o servidor fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`. A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione. Para obter mais informações, veja [Autenticação do Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Configura o nome de exibição mostrado aos usuários em páginas de logon. |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

**Modelo de hospedagem de fora do processo**

::: moniker-end

Para configurar opções do IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. O exemplo a seguir impede que o aplicativo preencha `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opção                         | Padrão | Configuração |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Se `true`, o [middleware de integração do IIS](#enable-the-iisintegration-components) define o `HttpContext.User` autenticado pela [Autenticação do Windows](xref:security/authentication/windowsauth). Se `false`, o middleware fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`. A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione. Saiba mais no tópico [Autenticação do Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Configura o nome de exibição mostrado aos usuários em páginas de logon. |
| `ForwardClientCertificate`     | `true`  | Se `true` e o cabeçalho da solicitação `MS-ASPNETCORE-CLIENTCERT` estiverem presentes, o `HttpContext.Connection.ClientCertificate` será populado. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

O [Middleware de integração do IIS](#enable-the-iisintegration-components), que configura o Middleware de cabeçalhos encaminhados, e o módulo do ASP.NET Core, são configurados para encaminhar o esquema (HTTP/HTTPS) e o endereço IP remoto de onde a solicitação foi originada. Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga. Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>Arquivo web.config

O arquivo *web.config* configura o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Criando, transformar e publicar o arquivo *Web.config* é tratado por um destino do MSBuild (`_TransformWebConfig`) quando o projeto é publicado. Este destino está presente nos destinos do SDK da Web (`Microsoft.NET.Sdk.Web`). O SDK é definido na parte superior do arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Se um arquivo *web.config* não estiver presente no projeto, ele será criado com o *processPath* e os *argumentos* corretos para configurar o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) e será transferido para o [resultado publicado](xref:host-and-deploy/directory-structure).

Se um arquivo *web.config* estiver presente no projeto, ele será transformado com o *processPath* e os *argumentos* corretos para configurar o Módulo do ASP.NET Core e será movido para o resultado publicado. A transformação não altera as definições de configuração do IIS no arquivo.

O arquivo *web.config* pode fornecer configurações adicionais do IIS que controlam módulos ativos do IIS. Para saber mais sobre os módulos do IIS que podem processar solicitações com aplicativos do ASP.NET Core, veja o tópico [Módulos do IIS](xref:host-and-deploy/iis/modules).

Para impedir que o SDK Web transforme o arquivo *web.config*, use a propriedade **\<IsTransformWebConfigDisabled>** no arquivo do projeto:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Ao impedir que o SDK Web transforme o arquivo, o *processPath* e os *argumentos* devem ser definidos manualmente pelo desenvolvedor. Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

### <a name="webconfig-file-location"></a>Local do arquivo web.config

Para configurar o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) corretamente, o arquivo *web.config* deve estar presente no caminho raiz do conteúdo (geralmente, o aplicativo base do caminho) do aplicativo implantado. Esse é o mesmo local que o caminho físico do site fornecido ao IIS. O arquivo *web.config* é necessário na raiz do aplicativo para habilitar a publicação de vários aplicativos usando a Implantação da Web.

Existem arquivos confidenciais no caminho físico do aplicativo, como *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (comentários da Documentação XML) e *\<assembly>.deps.json*. Quando o arquivo *web.config* estiver presente e o site for iniciado normalmente, o IIS não atenderá a esses arquivos confidenciais se eles forem solicitados. Se o arquivo *web.config* estiver ausente, nomeado incorretamente ou se não for possível configurar o site para inicialização normal, o IIS poderá servir arquivos confidenciais publicamente.

**O arquivo *web.config* deve estar presente na implantação em todos os momentos, nomeado corretamente e ser capaz de configurar o site para inicialização normal. Nunca remova o arquivo *web.config* de uma implantação de produção.**

### <a name="transform-webconfig"></a>Transformação do Web.config

Se você precisar transformar o *Web.config* em publicação (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), consulte <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Configuração do IIS

**Sistemas operacionais do Windows Server**

Habilite a função **Servidor Web (IIS)** e estabeleça serviços de função.

1. Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor**. Na etapa **Funções de Servidor**, marque a caixa de **Servidor Web (IIS)** .

   ![A função de Servidor Web IIS é selecionada na etapa Selecionar funções de servidor.](index/_static/server-roles-ws2016.png)

1. Após a etapa **Recursos**, a etapa **Serviços de função** é carregada para o servidor Web (IIS). Selecione os serviços de função do IIS desejados ou aceite os serviços de função padrão fornecidos.

   ![Os serviços de função padrão são selecionados na etapa Selecionar serviços de função.](index/_static/role-services-ws2016.png)

   **Autenticação do Windows (opcional)**  
   Para habilitar a Autenticação do Windows, expanda os nós a seguir: **Servidor Web** > **Segurança**. Selecione o recurso **Autenticação do Windows**. Saiba mais em [Autenticação do Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar autenticação do Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcional)**  
   O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior. Para habilitar WebSockets, expanda os nós a seguir: **Servidor Web** > **Desenvolvimento de Aplicativo**. Selecione o recurso **Protocolo WebSocket**. Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).

1. Continue para a etapa **Confirmação** para instalar os serviços e a função de servidor Web. Um comando server/IIS restart não será necessário após a instalação da função **Servidor Web (IIS)** .

**Sistemas operacionais Windows de área de trabalho**

Habilite o **Console de Gerenciamento do IIS** e os **Serviços na World Wide Web**.

1. Navegue para **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).

1. Abra o nó **Serviços de Informações da Internet**. Abra o nó **Ferramentas de Gerenciamento da Web**.

1. Marque a caixa de **Console de Gerenciamento do IIS**.

1. Marque a caixa de **Serviços na World Wide Web**.

1. Aceite os recursos padrão dos **Serviços na World Wide Web** ou personalize os recursos do IIS.

   **Autenticação do Windows (opcional)**  
   Para habilitar a Autenticação do Windows, expanda os nós a seguir: **Serviços na World Wide Web** > **Segurança**. Selecione o recurso **Autenticação do Windows**. Saiba mais em [Autenticação do Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar autenticação do Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcional)**  
   O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior. Para habilitar WebSockets, expanda os nós a seguir: **Serviços na World Wide Web** > **Recursos de Desenvolvimento de Aplicativos**. Selecione o recurso **Protocolo WebSocket**. Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).

1. Se a instalação do IIS exigir uma reinicialização, reinicie o sistema.

![O Console de Gerenciamento do IIS e os Serviços na World Wide Web estão selecionados em Recursos do Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalar o pacote de hospedagem do .NET Core

Instale o *pacote de hospedagem do .NET Core* no sistema de hospedagem. O pacote instala o Tempo de Execução .NET Core, a Biblioteca do .NET Core e o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module). O módulo permite que aplicativos do ASP.NET Core sejam executados por trás do IIS. Se o sistema não tiver uma conexão com a Internet, obtenha e instale os [Pacotes redistribuíveis do Microsoft Visual C++ 2015](https://www.microsoft.com/download/details.aspx?id=53840) antes de instalar o pacote de hospedagem do .NET Core.

> [!IMPORTANT]
> Se o pacote de hospedagem for instalado antes do IIS, a instalação do pacote deverá ser reparada. Execute o instalador do pacote de hospedagem novamente depois de instalar o IIS.
>
> Se o pacote de hospedagem for instalado após a instalação da versão de 64 bits (x64) do .NET Core, os SDKs poderão parecer estar ausentes ([Nenhum SDK do .NET Core foi detectado](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Para resolver o problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Download direto (versão atual)

Baixe o instalador usando o seguinte link:

[Instalador de pacote de hospedagem do .NET Core atual (download direto)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Versões anteriores do instalador

Para obter uma versão anterior do instalador:

1. Navegue até os [arquivos de downloads do .NET](https://www.microsoft.com/net/download/archives).
1. Em **.NET Core**, selecione a versão do .NET Core.
1. Na coluna **Executar aplicativos – Tempo de execução**, localize a linha da versão de tempo de execução do .NET Core desejada.
1. Baixe o instalador usando o link **Pacote de hospedagem e de tempo de execução**.

> [!WARNING]
> Alguns instaladores contêm versões de lançamento que atingiram o EOL (fim da vida útil) e não têm mais suporte da Microsoft. Para saber mais, confira a [política de suporte](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Instalar o pacote de hospedagem

1. Execute o instalador no servidor. Os parâmetros a seguir estão disponíveis ao executar o instalador por meio de um shell de comando do administrador:

   * `OPT_NO_ANCM=1` &ndash; Ignorar a instalação do Módulo do ASP.NET Core.
   * `OPT_NO_RUNTIME=1` &ndash; Ignorar a instalação do tempo de execução do .NET Core.
   * `OPT_NO_SHAREDFX=1` &ndash; Ignorar a instalação da Estrutura Compartilhada do ASP.NET (tempo de execução do ASP.NET).
   * `OPT_NO_X86=1` &ndash; Ignorar a instalação dos tempos de execução x86. Use esse parâmetro quando você souber que não hospedará aplicativos de 32 bits. Se houver uma possibilidade de hospedar aplicativos de 32 bits e 64 bits no futuro, não use esse parâmetro e instale ambos os tempos de execução.
   * `OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Desabilite a verificação para usar uma Configuração Compartilhada do IIS quando a configuração compartilhada (*applicationHost.config*) estiver no mesmo computador do que a instalação do IIS. *Disponível somente para instaladores do ASP.NET Core 2.2 ou Hosting Bundler posterior.* Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Reinicie o sistema ou execute **net stop was /y**, seguido por **net start w3svc** de um shell de comando. A reinicialização do IIS identifica uma alteração no CAMINHO do sistema, que é uma variável de ambiente, realizada pelo instalador.

> [!NOTE]
> Para obter informações sobre a Configuração Compartilhada do IIS, consulte [Módulo do ASP.NET Core com a Configuração Compartilhada do IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalar a Implantação da Web durante a publicação com o Visual Studio

Ao implantar aplicativos para servidores com [Implantação da Web](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), instale a versão mais recente da Implantação da Web no servidor. Para instalar a Implantação da Web, use o [WebPI (Web Platform Installer)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenha um instalador diretamente no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). O método preferencial é usar o WebPI. O WebPI oferece uma instalação autônoma e uma configuração para provedores de hospedagem.

## <a name="create-the-iis-site"></a>Criar o site do IIS

1. No sistema de hospedagem, crie uma pasta para conter arquivos e pastas publicados do aplicativo. Em uma etapa a seguir, o caminho da pasta é fornecido ao IIS como o caminho físico para o aplicativo. Para obter mais informações sobre o layout de arquivo e a pasta de implantação de um aplicativo, confira <xref:host-and-deploy/directory-structure>.

1. No Gerenciador do IIS, abra o nó do servidor no painel **Conexões**. Clique com botão direito do mouse na pasta **Sites**. Selecione **Adicionar Site** no menu contextual.

1. Forneça um **Nome do site** e defina o **Caminho físico** como a pasta de implantação do aplicativo. Forneça a configuração **Associação** e crie o site ao selecionar **OK**:

   ![Forneça o Nome do site, o caminho físico e o Nome do host na etapa Adicionar Site.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Associações de curinga de nível superior (`http://*:80/` e `http://+:80`) **não** devem ser usadas. Associações de curinga de nível superior podem abrir o aplicativo para vulnerabilidades de segurança. Isso se aplica a curingas fortes e fracos. Use nomes de host explícitos em vez de curingas. Associações de curinga de subdomínio (por exemplo, `*.mysub.com`) não têm esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável). Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.

1. No nó do servidor, selecione **Pools de Aplicativos**.

1. Clique com o botão direito do mouse no pool de aplicativos do site e selecione **Configurações Básicas** no menu contextual.

1. Na janela **Editar Pool de Aplicativos**, defina a **versão do CLR do .NET** como **Sem Código Gerenciado**:

   ![Defina Sem Código Gerenciado para a versão do CLR do .NET.](index/_static/edit-apppool-ws2016.png)

    O ASP.NET Core é executado em um processo separado e gerencia o tempo de execução. O ASP.NET Core não depende do carregamento do CLR de Área de trabalho (CLR do .NET)&mdash;o Core Common Language Runtime (CoreCLR) para o .NET Core é inicializado para hospedar o aplicativo no processo de trabalho. Definir a **versão do CLR do .NET** como **Sem Código Gerenciado** é opcional, porém recomendado.

1. *ASP.NET Core 2.2 ou posterior*: para uma [implantação autocontida](/dotnet/core/deploying/#self-contained-deployments-scd) de 64 bits (x64) que usa o [modelo de hospedagem em processo](#in-process-hosting-model), desabilite o pool de aplicativos para processos de 32 bits (x86).

   Na barra lateral **Ações** do Gerenciador do IIS > **Pools de Aplicativos**, selecione **Definir Padrões do Pool de Aplicativos** ou **Configurações Avançadas**. Localize **Habilitar Aplicativos de 32 bits** e defina o valor como `False`. Essa configuração não afeta os aplicativos implantados para a [hospedagem fora do processo](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Confirme se a identidade do modelo de processo tem as permissões apropriadas.

   Se você alterar a identidade padrão do pool de aplicativos (**Modelo de Processo** > **Identidade**) em **ApplicationPoolIdentity** para outra, verifique se a nova identidade tem as permissões necessárias para acessar a pasta do aplicativo, o banco de dados e outros recursos necessários. Por exemplo, o pool de aplicativos requer acesso de leitura e gravação às pastas nas quais o aplicativo lê e grava os arquivos.

**Configuração de Autenticação do Windows (opcional)**  
Para saber mais, veja [Configurar a Autenticação do Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Implantar o aplicativo

Implante o aplicativo na pasta **caminho físico** do IIS que foi estabelecida na seção [Criar o site do IIS](#create-the-iis-site). [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) é o mecanismo recomendado para implantação, mas existem várias opções para mover o aplicativo da pasta *publicar* do projeto para a pasta de implantação do sistema de hospedagem.

### <a name="web-deploy-with-visual-studio"></a>Implantação da Web com o Visual Studio

Consulte o tópico [Perfis de publicação do Visual Studio para implantação de aplicativos ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) para saber como criar um perfil de publicação para uso com a Implantação da Web. Se o provedor de hospedagem fornecer um Perfil de Publicação ou o suporte para a criação de um, baixe o perfil e importe-o usando a caixa de diálogo **Publicar** do Visual Studio:

![Página de caixa de diálogo Publicar](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Implantação da Web fora do Visual Studio

Também é possível usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) fora do Visual Studio na linha de comando. Para obter mais informações, consulte [Ferramenta de Implantação da Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativas à Implantação da Web

Use qualquer um dos vários métodos para mover o aplicativo para o sistema host, como cópia manual, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) ou [PowerShell](/powershell/).

Para obter mais informações sobre a implantação do ASP.NET Core no IIS, consulte a seção [Recursos de implantação para administradores do IIS](#deployment-resources-for-iis-administrators).

## <a name="browse-the-website"></a>Navegar no site

Depois de implantar o aplicativo no sistema de hospedagem, faça uma solicitação para um dos pontos de extremidade públicos do aplicativo.

No exemplo a seguir, o site está associado a um **Nome do Host** IIS de `www.mysite.com` na **Porta** `80`. É feita uma solicitação para `http://www.mysite.com`:

![O navegador Microsoft Edge carregou a página de inicialização do IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Arquivos de implantação bloqueados

Os arquivos na pasta de implantação são bloqueados quando o aplicativo está em execução. Os arquivos bloqueados não podem ser substituídos durante a implantação. Para liberar os arquivos bloqueados em uma implantação, interrompa o pool de aplicativos usando **uma** das abordagens a seguir:

* Use a Implantação da Web e referencie `Microsoft.NET.Sdk.Web` no arquivo do projeto. Um arquivo *app_offline.htm* é colocado na raiz do diretório de aplicativo da Web. Quando o arquivo estiver presente, o módulo do ASP.NET Core apenas desligará o aplicativo e servirá o arquivo *app_offline.htm* durante a implantação. Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Manualmente interrompa o pool de aplicativos no Gerenciador do IIS no servidor.
* Use o PowerShell para soltar *app_offline.html* (requer o PowerShell 5 ou posterior):

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Proteção de dados

A [pilha Proteção de Dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) ASP.NET Core, incluindo aqueles usados na autenticação. Mesmo se as APIs de proteção de dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada com um script de implantação ou no código do usuário para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente. Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.

Se o token de autenticação for armazenado na memória quando o aplicativo for reiniciado:

* Todos os tokens de autenticação baseados em cookies serão invalidados. 
* Os usuários precisam entrar novamente na próxima solicitação deles. 
* Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados. Isso pode incluir os [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [cookies TempData do MVC do ASP.NET Core](xref:fundamentals/app-state#tempdata).

Para configurar a proteção de dados no IIS para persistir o token de autenticação, use **uma** das seguintes abordagens:

* **Criar chaves de registro de proteção de dados**

  As chaves de proteção de dados usadas pelos aplicativos ASP.NET Core são armazenadas no registro externo aos aplicativos. Para persistir as chaves de determinado aplicativo, crie uma chave de registro para o pool de aplicativos.

  Para instalações autônomas do IIS não Web Farm, você pode usar o [script Provision-AutoGenKeys.ps1 de Proteção de Dados do PowerShell](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada pool de aplicativos usado com um aplicativo ASP.NET Core. Esse script cria uma chave de registro no registro HKLM que é acessível apenas para a conta do processo de trabalho do pool de aplicativos do aplicativo. As chaves são criptografadas em repouso usando a DPAPI com uma chave para todo o computador.

  Em cenários de web farm, um aplicativo pode ser configurado para usar um caminho UNC para armazenar seu token de autenticação de proteção de dados. Por padrão, as chaves de proteção de dados não são criptografadas. Garanta que as permissões de arquivo de o compartilhamento de rede sejam limitadas à conta do Windows na qual o aplicativo é executado. Um certificado X509 pode ser usado para proteger chaves em repouso. Considere um mecanismo para permitir aos usuários carregar certificados: coloque os certificados no repositório de certificados confiáveis do usuário e certifique-se de que eles estejam disponíveis em todos os computadores nos quais o aplicativo do usuário é executado. Veja [Configurar a proteção de dados do ASP.NET Core](xref:security/data-protection/configuration/overview) para obter detalhes.

* **Configurar o pool de aplicativos do IIS para carregar o perfil do usuário**

  Essa configuração está na seção **Modelo de processo** nas **Configurações avançadas** do pool de aplicativos. Defina **Carregar Perfil do Usuário** como `True`. Quando definido como `True`, as chaves são armazenadas no diretório do perfil do usuário e protegidas usando DPAPI com uma chave específica para a conta de usuário. As chaves são persistidas para a pasta *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.

  O [atributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) do pool de aplicativos também deve ser habilitado. O valor padrão de `setProfileEnvironment` é `true`. Em alguns cenários (por exemplo, um SO Windows), `setProfileEnvironment` é definido como `false`. Se as chaves não estiverem armazenadas no diretório do perfil do usuário como esperado:

  1. navegue até a pasta *%windir%/system32/inetsrv/config*.
  1. Abra o arquivo *applicationHost.config*.
  1. Localize o elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Confirme se o atributo `setProfileEnvironment` não está presente, que tem como padrão o valor `true`, ou defina explicitamente o valor do atributo como `true`.

* **Use o sistema de arquivos como um repositório de tokens de autenticação**

  Ajuste o código do aplicativo para [usar o sistema de arquivos como um repositório de tokens de autenticação](xref:security/data-protection/configuration/overview). Use um certificado X509 para proteger o token de autenticação e verifique se ele é um certificado confiável. Se o certificado for autoassinado, você deverá colocá-lo no repositório Raiz confiável.

  Ao usar o IIS em uma web farm:

  * Use um compartilhamento de arquivos que pode ser acessado por todos os computadores.
  * Implante um certificado X509 em cada computador. Configure a [proteção de dados no código](xref:security/data-protection/configuration/overview).

* **Definir uma política para todo o computador para proteção de dados**

  O sistema de proteção de dados tem suporte limitado para a configuração da [política de todo o computador](xref:security/data-protection/configuration/machine-wide-policy) padrão para todos os aplicativos que consomem as APIs de proteção de dados. Para obter mais informações, consulte <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Diretórios virtuais

[Diretórios virtuais IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) não são compatíveis com aplicativos ASP.NET Core. Um aplicativo pode ser hospedado como um [subaplicativo](#sub-applications).

## <a name="sub-applications"></a>Subaplicativos

Um aplicativo ASP.NET Core pode ser hospedado como um [subaplicativo IIS (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). A parte do caminho do subaplicativo se torna parte da URL raiz do aplicativo.

::: moniker range="< aspnetcore-2.2"

Um subaplicativo não deve incluir o módulo do ASP.NET Core como um manipulador. Se o módulo for adicionado como um manipulador em um arquivo *web.config* de um subaplicativo, quando tentar procurar o subaplicativo, você receberá um *500.19 Erro Interno do Servidor* que referenciará o arquivo de configuração com falha.

O seguinte exemplo mostra um arquivo *web.config* publicado de um subaplicativo ASP.NET Core:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Ao hospedar um subaplicativo não ASP.NET Core abaixo de um aplicativo ASP.NET Core, remova explicitamente o manipulador herdado no arquivo *web.config* do subaplicativo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

Links de ativos estáticos dentro do subaplicativo devem usar a notação de sinal de til e barra (`~/`). A notação de sinal de til e barra aciona um [Auxiliar de Marca](xref:mvc/views/tag-helpers/intro) para preceder a base de caminho do subaplicativo ao link relativo renderizado. Para um subaplicativo no `/subapp_path`, uma imagem vinculada com `src="~/image.png"` é renderizada como `src="/subapp_path/image.png"`. O Middleware de Arquivo Estático do aplicativo raiz não processa a solicitação de arquivo estático. A solicitação é processada pelo Middleware de Arquivo Estático do subaplicativo.

Se um atributo de ativo estático `src` for definido como um caminho absoluto (por exemplo, `src="/image.png"`), o link será renderizado sem a base de caminho do subaplicativo. O Middleware de Arquivos Estáticos do aplicativo raiz tenta fornecer o ativo do [webroot](xref:fundamentals/index#web-root) da raiz do aplicativo, que resulta em uma resposta *404 – Não encontrado*, a menos que o ativo estático esteja disponível no aplicativo raiz.

Para hospedar um aplicativo ASP.NET Core como um subaplicativo em outro aplicativo do ASP.NET Core:

1. Estabeleça um pool de aplicativos para o subaplicativo. Defina a **versão do CLR do .NET** como **Sem Código Gerenciado** porque o Core Common Language Runtime (CoreCLR) do .NET Core é inicializado para hospedar o aplicativo no processo de trabalho, não no CLR de Área de trabalho (CLR do .NET).

1. Adicione o site raiz no Gerenciador do IIS com o subaplicativo em uma pasta no site raiz.

1. Clique com o botão direito do mouse na pasta do subaplicativo no Gerenciador do IIS e selecione **Converter em aplicativo**.

1. Na caixa de diálogo **Adicionar Aplicativo**, use o botão **Selecionar** no **Pool de Aplicativos** para atribuir o pool de aplicativos que você criou ao subaplicativo. Selecione **OK**.

A atribuição de um pool de aplicativos separado para o subaplicativo é um requisito ao usar o modelo de hospedagem em processo.

Para obter mais informações sobre o modelo de hospedagem em processo e como configurar o módulo do ASP.NET Core, confira <xref:host-and-deploy/aspnet-core-module> e <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Configuração do IIS com web.config

A configuração do IIS é influenciada pela seção `<system.webServer>` do *web.config* para cenários do IIS que são funcionais para aplicativos ASP.NET Core com o Módulo do ASP.NET Core. Por exemplo, a configuração do IIS é funcional para a compactação dinâmica. Se o IIS for configurado no nível do servidor para usar a compactação dinâmica, o elemento `<urlCompression>` no arquivo *web.config* do aplicativo pode desabilitá-la para um aplicativo do ASP.NET Core.

Para saber mais, veja a [referência de configuração do \<system.webServer>](/iis/configuration/system.webServer/), a [referência de configuração do Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) e [Módulos do IIS com o ASP.NET Core](xref:host-and-deploy/iis/modules). Para definir variáveis de ambiente para aplicativos individuais executados em pools de aplicativos isolados (compatível com o IIS 10.0+ ou posterior), veja a seção *comando AppCmd.exe* do tópico [Variáveis de ambiente \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) na documentação de referência do IIS.

## <a name="configuration-sections-of-webconfig"></a>Seções de configuração de web.config

As seções de configuração de aplicativos ASP.NET 4.x em *web.config* não são usadas por aplicativos ASP.NET Core para configuração:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplicativos ASP.NET Core são configurados para usar outros provedores de configuração. Para obter mais informações, consulte [Configuração](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pools de aplicativos

::: moniker range=">= aspnetcore-2.2"

O isolamento do pool de aplicativos é determinado pelo modelo de hospedagem:

* Hospedagem em processo &ndash; Aplicativos são necessários para execução em pools de aplicativos separados.
* Hospedagem fora do processo &ndash; É recomendável isolar os aplicativos uns dos outros, executando cada aplicativo em seu próprio pool de aplicativos.

A caixa de diálogo **Adicionar Site** do IIS usa como padrão um único pool de aplicativos por aplicativo. Quando um **Nome de site** é fornecido, o texto é transferido automaticamente para a caixa de texto **Pool de aplicativos**. Um novo pool de aplicativos é criado usando o nome do site quando você adicionar o site.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ao hospedar vários sites em um servidor, é recomendável isolar os aplicativos uns dos outros, executando cada aplicativo em seu próprio pool de aplicativo. A caixa de diálogo **Adicionar Site** do IIS usa como padrão essa configuração. Quando um **Nome de site** é fornecido, o texto é transferido automaticamente para a caixa de texto **Pool de aplicativos**. Um novo pool de aplicativos é criado usando o nome do site quando você adicionar o site.

::: moniker-end

## <a name="application-pool-identity"></a>Identidade do pool de aplicativos

Uma conta de identidade do pool de aplicativos permite executar um aplicativo em uma conta exclusiva sem a necessidade de criar e gerenciar domínios ou contas locais. No IIS 8.0 ou posterior, o WAS (Processo de trabalho do administrador) do IIS cria uma conta virtual com o nome do novo pool de aplicativos e executa os processos de trabalho do pool de aplicativos nesta conta por padrão. No Console de Gerenciamento do IIS, em **Configurações avançadas** do pool de aplicativos, verifique se a **Identidade** é definida para usar **ApplicationPoolIdentity**:

![Caixa de diálogo Configurações avançadas do pool de aplicativos](index/_static/apppool-identity.png)

O processo de gerenciamento do IIS cria um identificador seguro com o nome do pool de aplicativos no Sistema de segurança do Windows. Os recursos podem ser protegidos usando essa identidade. No entanto, essa identidade não é uma conta de usuário real e não será mostrada no Console de Gerenciamento de Usuários do Windows.

Se o processo de trabalho do IIS requerer acesso elevado ao aplicativo, modifique a ACL (lista de controle de acesso) do diretório que contém o aplicativo:

1. Abra o Windows Explorer e navegue para o diretório.

1. Clique com o botão direito do mouse no diretório e selecione **Propriedades**.

1. Na guia **Segurança**, selecione o botão **Editar** e, em seguida, no botão **Adicionar**.

1. Clique no botão **Locais** e verifique se o sistema está selecionado.

1. Insira **IIS AppPool\\<nome_pool_aplicativos>** na área **Inserir os nomes de objeto a serem selecionados**. Selecione o botão **Verificar Nomes**. Para o *DefaultAppPool*, verifique os nomes usando **IIS AppPool\DefaultAppPool**. Quando o botão **Verificar Nomes** é selecionado, um valor de **DefaultAppPool** é indicado na área de nomes de objeto. Não é possível inserir o nome do pool de aplicativos diretamente na área de nomes de objeto. Use o formato **IIS AppPool\\<nome_pool_aplicativos>** ao verificar o nome do objeto.

   ![Caixa de diálogo Selecionar usuários ou grupos da pasta do aplicativo: o nome do pool de aplicativos "DefaultAppPool" é anexado ao "IIS AppPool\" na área de nomes de objeto, antes de escolher "Verificar Nomes".](index/_static/select-users-or-groups-1.png)

1. Selecione **OK**.

   ![Caixa de diálogo Selecionar usuários ou grupos da pasta do aplicativo: depois de escolher "Verificar Nomes", o nome do objeto "DefaultAppPool" é mostrado na área de nomes de objeto.](index/_static/select-users-or-groups-2.png)

1. As permissões de leitura &amp; execução devem ser concedidas por padrão. Forneça permissões adicionais conforme necessário.

O acesso também pode ser concedido por meio de um prompt de comando usando a ferramenta **ICACLS**. Usando o *DefaultAppPool* como exemplo, o comando a seguir é usado:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Para saber mais, veja o tópico [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="http2-support"></a>Compatibilidade com HTTP/2

::: moniker range=">= aspnetcore-2.2"

O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação de IIS:

* Em processo
  * Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
  * Conexão TLS 1.2 ou posterior
* Fora do processo
  * Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
  * Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o [servidor Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.
  * Conexão TLS 1.2 ou posterior

Para uma implantação em processo quando uma conexão HTTP/2 for estabelecida, o [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`. Para uma implantação fora de processo quando uma conexão HTTP/2 for estabelecida, o [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/1.1`.

Para saber mais sobre os modelos de hospedagem em processo e fora de processo, confira <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com implantações fora de processo que cumprem os seguintes requisitos básicos:

* Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
* Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o [servidor Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.
* Estrutura de destino: não se aplica a implantações fora de processo, visto que a conexão HTTP/2 é manipulada inteiramente pelo IIS.
* Conexão TLS 1.2 ou posterior

Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/1.1`.

::: moniker-end

O HTTP/2 está habilitado por padrão. As conexões retornarão para HTTP/1.1 se uma conexão HTTP/2 não for estabelecida. Para obter mais informações sobre a configuração de HTTP/2 com implantações do IIS, consulte [HTTP/2 no IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Solicitações de simulação do CORS

*Esta seção só se aplica a aplicativos ASP.NET Core com o .NET Framework como destino.*

Para um aplicativo ASP.NET Core com o .NET Framework como destino, as solicitações OPTIONS não são passadas para o aplicativo por padrão no IIS. Confira como configurar os manipuladores IIS do aplicativo no *Web.config* para passar as solicitações OPTIONS em [Habilitar solicitações entre origens na ASP.NET Web API 2: Como funciona o CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization-module-and-idle-timeout"></a>Módulo de Inicialização de Aplicativo e Tempo Limite de Ociosidade

Quando hospedado no IIS pela versão 2 do Módulo do ASP.NET Core:

* [Módulo de Inicialização de Aplicativo](#application-initialization-module) &ndash; Aplicativos hospedados [em processo](#in-process-hosting-model) ou [fora do processo](#out-of-process-hosting-model) podem ser configurados para iniciar automaticamente em uma reinicialização do servidor ou do processo de trabalho.
* [Tempo Limite de Ociosidade](#idle-timeout) &ndash; Aplicativos hospedados [em processo](#in-process-hosting-model) podem ser configurados para não atingir o tempo limite durante períodos de inatividade.

### <a name="application-initialization-module"></a>Módulo de Inicialização de Aplicativo

*Aplica-se a aplicativos hospedados em processo e fora de processo.*

[Inicialização de Aplicativo IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) é um recurso do IIS que envia uma solicitação HTTP para o aplicativo quando o pool de aplicativos é iniciado ou reciclado. A solicitação dispara a inicialização do aplicativo. Por padrão, o IIS emite uma solicitação para a URL raiz do aplicativo (`/`) para inicializar o aplicativo (confira mais detalhes sobre a configuração nos [recursos adicionais](#application-initialization-module-and-idle-timeout-additional-resources)).

Confirme se o recurso da função Inicialização de Aplicativo do IIS está habilitado:

No Windows 7 ou sistemas de área de trabalho posteriores, ao usar o IIS localmente:

1. Navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).
1. Abra **Serviços de Informações da Internet** > **Serviços da World Wide Web** > **Recursos de Desenvolvimento de Aplicativos**.
1. Marque a caixa de seleção **Inicialização de Aplicativo**.

No Windows Server 2008 R2 ou posterior:

1. Abra o **Assistente de Adição de Funções e Recursos**.
1. No painel **Selecionar serviços de função**, abra o nó **Desenvolvimento de aplicativos**.
1. Marque a caixa de seleção **Inicialização de Aplicativo**.

Use quaisquer das abordagens a seguir para habilitar o Módulo de Inicialização do Aplicativo para o site:

* Usando o Gerenciador do IIS:

  1. Selecione **Pools de Aplicativos** no painel **Conexões**.
  1. Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas**.
  1. O **Modo de Inicialização** padrão é **OnDemand**. Defina o **Modo de Inicialização** como **AlwaysRunning**. Selecione **OK**.
  1. Abra o nó **Sites** no painel **Conexões**.
  1. Clique com o botão direito do mouse no aplicativo e selecione **Gerenciar Site** > **Configurações Avançadas**.
  1. A configuração de **Pré-carregamento Habilitado** padrão é **Falso**. Defina **Pré-carregamento Habilitado** como **Verdadeiro**. Selecione **OK**.

* Usando o *web.config*, adicione o elemento `<applicationInitialization>` definindo `doAppInitAfterRestart` como `true` aos elementos `<system.webServer>` no arquivo *web.config* do aplicativo:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Tempo Limite de Ociosidade

*Só se aplica a aplicativos hospedados em processo.*

Para impedir que o aplicativo fique ocioso, defina o tempo limite de ociosidade do pool de aplicativos, usando o Gerenciador do IIS:

1. Selecione **Pools de Aplicativos** no painel **Conexões**.
1. Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas**.
1. O **Tempo Limite de Ociosidade (minutos)** é de **20** minutos. Defina o **Tempo Limite de Ociosidade (minutos)** como **0** (zero). Selecione **OK**.
1. Recicle o processo de trabalho.

Para impedir que aplicativos hospedados [fora de processo](#out-of-process-hosting-model) atinjam o tempo limite, use uma das seguintes abordagens:

* Execute ping do aplicativo de um serviço externo para mantê-lo em execução.
* Se o aplicativo hospedar apenas serviços em segundo plano, evite a hospedagem do IIS e use um [Serviço do Windows para hospedar o aplicativo ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Recursos adicionais do Módulo de Inicialização de Aplicativo e do Tempo Limite de Ociosidade

* [Inicialização de Aplicativo do IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicialização do aplicativo \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).
* [Configurações de modelo de processo para um Pool de aplicativos \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

::: moniker-end

## <a name="deployment-resources-for-iis-administrators"></a>Recursos de implantação para administradores do IIS

Conheça o ISS detalhadamente na documentação do IIS.  
[Documentação do ISS](/iis)

Saiba mais sobre os modelos de implantação de aplicativos do .NET Core.  
[Implantação de aplicativos do .NET Core](/dotnet/core/deploying/)

Saiba como o módulo do ASP.NET Core permite que o servidor Web Kestrel use o IIS ou o IIS Express como um servidor proxy reverso.  
[Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module)

Saiba como configurar o módulo do ASP.NET Core para hospedar aplicativos do ASP.NET Core.  
[Referência de configuração do Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module)

Saiba mais sobre a estrutura do diretório de aplicativos publicados do ASP.NET Core.  
[Estrutura de diretórios](xref:host-and-deploy/directory-structure)

Descubra módulos ativos e inativos do IIS para aplicativos do ASP.NET Core e como gerenciar os módulos do IIS.  
[Módulos do IIS](xref:host-and-deploy/iis/modules)

Saiba como diagnosticar problemas com as implantações do ISS dos aplicativos do ASP.NET Core.  
[Solução de problemas](xref:test/troubleshoot-azure-iis)

Distinga erros comuns ao hospedar aplicativos do ASP.NET Core no IIS.  
[Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS](xref:host-and-deploy/azure-iis-errors-reference)

## <a name="additional-resources"></a>Recursos adicionais

* <xref:test/troubleshoot>
* [Introdução ao ASP.NET Core](xref:index)
* [O site oficial da IIS da Microsoft](https://www.iis.net/)
* [Biblioteca de conteúdo técnico do Windows Server](/windows-server/windows-server)
* [HTTP/2 no IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>
