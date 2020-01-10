---
title: Implantar aplicativos ASP.NET Core no Serviço de Aplicativo do Azure
author: bradygaster
description: Este artigo contém links para o host do Azure e para implantar recursos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 286d73d732b146fef15bbfc309caeb214cdbbe0d
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829173"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Implantar aplicativos ASP.NET Core no Serviço de Aplicativo do Azure

[Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) é um [serviço de plataforma de computação em nuvem da Microsoft](https://azure.microsoft.com/) para hospedar aplicativos Web, incluindo o ASP.NET Core.

## <a name="useful-resources"></a>Recursos úteis

A [Documentação de Serviço de Aplicativo](/azure/app-service/) do Azure é a página inicial para documentação de Aplicativos Azure, tutoriais, exemplos, guias de instruções e outros recursos. Dois tutoriais importantes que pertencem à hospedagem de aplicativos ASP.NET Core são:

[Criar um aplicativo Web ASP.NET Core no Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Use o Visual Studio para criar e implantar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure no Windows.

[Criar um aplicativo ASP.NET Core no Serviço de Aplicativo no Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Use a linha de comando do Visual Studio para criar e implantar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure no Linux.

Consulte o [ASP.NET Core no painel do serviço de aplicativo](https://aspnetcoreon.azurewebsites.net/) para obter a versão do ASP.NET Core disponível no serviço Azure app.

Assine o repositório de [anúncios do serviço de aplicativo](https://github.com/Azure/app-service-announcements/) e monitore os problemas. A equipe do serviço de aplicativo publica regularmente anúncios e cenários chegando ao serviço de aplicativo.

Os artigos a seguir estão disponíveis na documentação do ASP.NET Core:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Saiba como criar um aplicativo Web ASP.NET Core usando o Visual Studio e implantá-lo no Serviço de Aplicativo do Azure, usando o Git para implantação contínua.

[Crie seu primeiro pipeline](/azure/devops/pipelines/get-started-yaml)  
Configurar um build de CI para um aplicativo ASP.NET Core e, em seguida, criar uma versão de implantação contínua para o Serviço de Aplicativo do Azure.

[Área restrita de aplicativo Web do Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Descubra as limitações de runtime do Serviço de Aplicativo do Azure impostas pela plataforma de Aplicativos do Azure.

<xref:test/troubleshoot>  
Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.

## <a name="application-configuration"></a>Configuração do aplicativo

### <a name="platform"></a>Platform

A arquitetura da plataforma (x86/x64) de um aplicativo dos serviços de aplicativos é definida nas configurações do aplicativo no portal do Azure para aplicativos que são hospedados em uma camada de hospedagem superior (básica) ou mais alta. Confirme se as configurações de publicação do aplicativo (por exemplo, no perfil de publicação do Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) correspondem à configuração na configuração de serviço do aplicativo no portal do Azure.

::: moniker range=">= aspnetcore-2.2"

Os runtimes para aplicativos de 32 bits (x86) e 64 bits (x64) estão presentes no Serviço de Aplicativo do Azure. O [SDK do .NET Core](/dotnet/core/sdk) disponível no Serviço de Aplicativo é de 32 bits, mas é possível implantar aplicativos de 64 bits compilados localmente usando o console do [Kudu](https://github.com/projectkudu/kudu/wiki) ou o processo de publicação do Visual Studio. Para obter mais informações, consulte a seção [Publicar e implantar o aplicativo](#publish-and-deploy-the-app).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Para aplicativos com dependências nativas, os runtimes para aplicativos de 32 bits (x86) estão presentes no Serviço de Aplicativo do Azure. O [SDK do .NET Core](/dotnet/core/sdk) disponível no Serviço de Aplicativo é de 32 bits.

::: moniker-end

Para obter mais informações sobre os componentes do .NET Core Framework e os métodos de distribuição, como informações sobre o tempo de execução do .NET Core e o SDK do .NET Core, consulte [sobre o .NET Core: composição](/dotnet/core/about#composition).

### <a name="packages"></a>Pacotes

Incluem os seguintes pacotes NuGet para fornecer recursos de registro automático em log para aplicativos implantados no Serviço de Aplicativo do Azure:

* O [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) usa [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) para fornecer integração leve do ASP.NET Core com o Serviço de Aplicativo do Azure. Os recursos de registro em log adicionais são fornecidos pelo pacote `Microsoft.AspNetCore.AzureAppServicesIntegration`.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executa [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) para adicionar provedores de log de diagnósticos do Serviço de Aplicativo do Azure no pacote `Microsoft.Extensions.Logging.AzureAppServices`.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) fornece implementações de agente para dar suporte a recursos de streaming de log e logs de diagnóstico do Serviço de Aplicativo do Azure.

Os pacotes anteriores não estão disponíveis no [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app). Os aplicativos que são direcionados ao .NET Framework ou fazem referência a um metapacote `Microsoft.AspNetCore.App` precisam referenciar explicitamente os pacotes individuais no arquivo de projeto do aplicativo.

## <a name="override-app-configuration-using-the-azure-portal"></a>Substituir a configuração do aplicativo no Portal do Azure

As configurações do aplicativo no portal do Azure permitem definir variáveis de ambiente para o aplicativo. As variáveis de ambiente podem ser consumidas pelo [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Quando uma configuração de aplicativo é criada ou modificada no Portal do Azure e o botão **Salvar** é selecionado, o Aplicativo Azure é reiniciado. A variável de ambiente estará disponível para o aplicativo após o serviço ser reiniciado.

::: moniker range=">= aspnetcore-3.0"

Quando um aplicativo usa o [host genérico](xref:fundamentals/host/generic-host), as variáveis de ambiente são carregadas na configuração do aplicativo quando <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> é chamado para criar o host. Para saber mais, confira <xref:fundamentals/host/generic-host> e o [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Quando um aplicativo usa o [host da Web](xref:fundamentals/host/web-host), as variáveis de ambiente são carregadas na configuração do aplicativo quando <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> é chamado para criar o host. Para saber mais, confira <xref:fundamentals/host/web-host> e o [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

O [Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), que configura o Middleware de cabeçalhos encaminhados ao hospedar [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), e o módulo do ASP.NET Core são configurados para encaminhar o esquema (HTTP/HTTPS) e o endereço IP remoto de onde a solicitação foi originada. Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga. Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Monitoramento e registro em log

::: moniker range=">= aspnetcore-3.0"

Os aplicativos ASP.NET Core implantados no Serviço de Aplicativo recebem automaticamente uma extensão do Serviço de Aplicativo, **Integração de Log do ASP.NET Core**. A extensão habilita a integração de log para aplicativos ASP.NET Core no Serviço de Aplicativo do Azure.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Os aplicativos ASP.NET Core implantados no Serviço de Aplicativo recebem automaticamente uma extensão do Serviço de Aplicativo, **Extensões de Log do ASP.NET Core**. A extensão habilita a integração de log para aplicativos ASP.NET Core no Serviço de Aplicativo do Azure.

::: moniker-end

Para monitoramento, registro em log e informações de solução de problemas, veja os seguintes artigos:

[Monitore aplicativos no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-monitor)  
Saiba como examinar as cotas e métricas para aplicativos e planos do Serviço de Aplicativo.

[Habilite log de diagnósticos para aplicativos no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log)  
Descubra como habilitar e acessar o log de diagnósticos para os códigos de status HTTP, solicitações com falha e atividade do servidor Web.

<xref:fundamentals/error-handling>  
Entenda as abordagens comuns para o tratamento de erros em aplicativos ASP.NET Core.

<xref:test/troubleshoot-azure-iis>  
Saiba como diagnosticar problemas com implantações do Serviço de Aplicativo do Azure com aplicativos ASP.NET Core.

<xref:host-and-deploy/azure-iis-errors-reference>  
Consulte os erros comuns de configuração de implantação para aplicativos hospedados pelo Serviço de Aplicativo do Azure/IIS com orientação para solução de problemas.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Anel de chave de proteção de dados e slots de implantação

[Chaves de proteção de dados](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) são mantidas na pasta *%HOME%\ASP.NET\DataProtection-Keys*. Essa pasta é apoiada pelo repositório de rede e é sincronizada em todos os computadores que hospedam o aplicativo. As chaves não são protegidas em repouso. Essa pasta fornece o anel de chave para todas as instâncias de um aplicativo em um único slot de implantação. Slots de implantação separados, como de preparo e produção, não compartilham um anel de chave.

Quando ocorre a troca entre os slots de implantação, nenhum sistema que usa a proteção de dados consegue descriptografar dados armazenados usando o anel de chave dentro do slot anterior. O middleware de cookie do ASP.NET usa a proteção de dados para proteger seus cookies. Com isso, os usuários são desconectados de um aplicativo que usa o Middleware de Cookie padrão do ASP.NET. Para uma solução de anel de chave independente de slot, use um provedor de anel de chave externo, como:

* Armazenamento do Blobs do Azure
* Azure Key Vault
* Repositório SQL
* Cache redis

Para obter mais informações, consulte <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a>Implantar um aplicativo ASP.NET Core que usa uma versão prévia do .NET Core

Para implantar um aplicativo que usa uma versão de visualização do .NET Core, consulte os recursos a seguir. Essas abordagens também são usadas quando o tempo de execução está disponível, mas o SDK não foi instalado no serviço Azure App.

* [Especifique a versão de SDK do .NET Core usando Azure Pipelines](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Implantar um aplicativo de visualização independente](#deploy-a-self-contained-preview-app)
* [Usar o Docker com aplicativos Web para contêineres](#use-docker-with-web-apps-for-containers)
* [Instalar a extensão de site de visualização](#install-the-preview-site-extension)

Consulte o [ASP.NET Core no painel do serviço de aplicativo](https://aspnetcoreon.azurewebsites.net/) para obter a versão do ASP.NET Core disponível no serviço Azure app.

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Especifique a versão de SDK do .NET Core usando Azure Pipelines

Use [Azure app cenários de CI/CD de serviço](/azure/app-service/deploy-continuous-deployment) para configurar uma compilação de integração contínua com o Azure DevOps. Depois que a compilação DevOps do Azure for criada, configure opcionalmente a compilação para usar uma versão específica do SDK. 

#### <a name="specify-the-net-core-sdk-version"></a>Especificar a versão de SDK do .NET Core

Ao usar o centro de implantação do serviço de aplicativo para criar uma compilação DevOps do Azure, o pipeline de compilação padrão inclui etapas para `Restore`, `Build`, `Test`e `Publish`. Para especificar a versão do SDK, selecione o botão **Adicionar (+)** na lista de trabalhos do agente para adicionar uma nova etapa. Pesquise **SDK do .NET Core** na barra de pesquisa. 

![Adicionar a etapa de SDK do .NET Core](index/add-sdk-step.png)

Mova a etapa para a primeira posição na compilação para que as etapas a seguir usem a versão especificada do SDK do .NET Core. Especifique a versão do SDK do .NET Core. Neste exemplo, o SDK é definido como `3.0.100`.

![Etapa do SDK concluída](index/sdk-step-first-place.png)

Para publicar uma [SCD (implantação autônoma)](/dotnet/core/deploying/#self-contained-deployments-scd), configure o SCD na etapa `Publish` e forneça o [RID (identificador de tempo de execução)](/dotnet/core/rid-catalog).

![Publicação independente](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Implantar um aplicativo autossuficiente em versão prévia

Uma [SCD (implantação autocontida)](/dotnet/core/deploying/#self-contained-deployments-scd) voltada para um runtime de versão prévia transporta o runtime da versão prévia na implantação.

Ao implantar um aplicativo autocontido:

* O site no Serviço de Aplicativo do Azure não exige a [extensão de site da versão prévia](#install-the-preview-site-extension).
* O aplicativo precisa ser publicado após uma abordagem diferente da publicação em uma [FDD (implantação dependente de estrutura)](/dotnet/core/deploying#framework-dependent-deployments-fdd).

Siga as orientações fornecidas na seção [Implantar o aplicativo autossuficiente](#deploy-the-app-self-contained).

### <a name="use-docker-with-web-apps-for-containers"></a>Usar o Docker com aplicativos Web para contêineres

O [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contém as imagens de versão prévia do Docker mais recentes. As imagens podem ser usadas como uma imagem de base. Use a imagem e implante aplicativos Web para contêineres normalmente.

### <a name="install-the-preview-site-extension"></a>Instalar a extensão de site de visualização

Se ocorrer um problema usando a extensão de site de visualização, abra um [problema dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).

1. No portal do Azure, navegue até o Serviço de Aplicativo.
1. Selecione o aplicativo Web.
1. Insira "ex" na caixa de pesquisa para filtrar por "Extensões" ou role para baixo na lista de ferramentas de gerenciamento.
1. Selecione **Extensões**.
1. Selecione **Adicionar**.
1. Selecione a extensão **Runtime do ASP.NET Core {X.Y} ({x64|x86})** na lista, em que `{X.Y}` é a versão prévia do ASP.NET Core e `{x64|x86}` especifica a plataforma.
1. Selecione **OK** para aceitar os termos legais.
1. Selecione **OK** para instalar a extensão.

Quando a operação for concluída, a versão prévia mais recente do .NET Core será instalada. Verifique a instalação:

1. Selecione **Ferramentas Avançadas**.
1. Selecione **Acessar** em **Ferramentas Avançadas**.
1. Selecione o item de menu **Console de depuração** > **PowerShell**.
1. No prompt do PowerShell, execute o seguinte comando. Substitua a versão do runtime do ASP.NET Core por `{X.Y}` e a plataforma por `{PLATFORM}` no comando:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   O comando retornará `True` quando o runtime da versão prévia x64 estiver instalado.

> [!NOTE]
> A arquitetura da plataforma (x86/x64) de um aplicativo dos serviços de aplicativos é definida nas configurações do aplicativo no portal do Azure para aplicativos que são hospedados em uma camada de hospedagem superior (básica) ou mais alta. Confirme se as configurações de publicação do aplicativo (por exemplo, no perfil de publicação do Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) correspondem à configuração na configuração de serviço do aplicativo no portal do Azure.
>
> Se o aplicativo for executado no modo em processo e a arquitetura da plataforma estiver configurada para 64 bits (x64), o Módulo do ASP.NET Core usará o runtime da versão prévia de 64 bits, se estiver presente. Instale a extensão de **tempo de execução do ASP.NET Core {X. Y} (x64)** usando o portal do Azure.
>
> Depois de instalar o tempo de execução x64 Preview, execute o seguinte comando na janela de comando do Azure kudu PowerShell para verificar a instalação. Substitua a versão de tempo de execução ASP.NET Core por `{X.Y}` no seguinte comando:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> O comando retornará `True` quando o runtime da versão prévia x64 estiver instalado.

> [!NOTE]
> As **Extensões do ASP.NET Core** habilitam uma funcionalidade adicional para o ASP.NET Core nos Serviços de Aplicativo do Azure, como a habilitação do registro em log do Azure. A extensão é instalada automaticamente durante a implantação do Visual Studio. Se a extensão não estiver instalada, instale-a para o aplicativo.

**Usar a extensão de site de visualização com um modelo do ARM**

Se um modelo do ARM for usado para criar e implantar aplicativos, o tipo de recurso `siteextensions` poderá ser usado para adicionar a extensão de site a um aplicativo Web. Por exemplo:

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Publicar e implantar o aplicativo

::: moniker range=">= aspnetcore-2.2"

Para uma implantação de 64 bits:

* Use um SDK do .NET Core de 64 bits para compilar um aplicativo de 64 bits.
* Configure a **Plataforma** como **64 bits** na seção **Configuração** > **Configurações gerais** do Serviço de Aplicativo. O aplicativo deve usar um plano de serviço Básico ou superior para possibilitar a escolha do número de bits da plataforma.

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a>Implantar o aplicativo dependente de estrutura de aplicativos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selecione **Compilar** > **Publicar {Application Name}** na barra de ferramentas do Visual Studio ou clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar**.
1. Na caixa de diálogo **Escolher um destino de publicação**, confirme se o **Serviço de Aplicativo** está selecionado.
1. Selecione **Avançado**. A caixa de diálogo **Publicar** será aberta.
1. Na caixa de diálogo **Publicar**:
   * Confirme se a configuração **Versão** está selecionada.
   * Abra a lista suspensa **Modo de Implantação** e selecione **Dependente de Estrutura**.
   * Selecione **Portátil** como o **Runtime de Destino**.
   * Se você precisar remover arquivos adicionais após a implantação, abra as **Opções de Publicação do Arquivo** e marque a caixa de seleção para remover arquivos adicionais no destino.
   * Selecione **Salvar**.
1. Crie um novo site ou atualize um site existente seguindo as solicitações restantes do assistente de publicação.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. No arquivo de projeto, não especifique um [RID (Identificador de Runtime)](/dotnet/core/rid-catalog).

1. Em um shell de comando, publique o aplicativo na Configuração de versão usando o comando [dotnet publish](/dotnet/core/tools/dotnet-publish). No exemplo a seguir, o aplicativo é publicado como dependente de estrutura:

   ```console
   dotnet publish --configuration Release
   ```

1. Mova o conteúdo do diretório *bin/Release/{TARGET FRAMEWORK}/publish* para o site no Serviço de Aplicativo. Se for arrastar o conteúdo da pasta *publish* de seu disco rígido local ou do compartilhamento de rede diretamente para o Serviço de Aplicativo no console do [Kudu](https://github.com/projectkudu/kudu/wiki), arraste os arquivos para a pasta `D:\home\site\wwwroot` no console do Kudu.

---

### <a name="deploy-the-app-self-contained"></a>Implantar o aplicativo autocontido

Use o Visual Studio ou as ferramentas da CLI (interface de linha de comando) para uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selecione **Compilar** > **Publicar {Application Name}** na barra de ferramentas do Visual Studio ou clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar**.
1. Na caixa de diálogo **Escolher um destino de publicação**, confirme se o **Serviço de Aplicativo** está selecionado.
1. Selecione **Avançado**. A caixa de diálogo **Publicar** será aberta.
1. Na caixa de diálogo **Publicar**:
   * Confirme se a configuração **Versão** está selecionada.
   * Abra a lista suspensa **Modo de Implantação** e selecione **Autocontido**.
   * Selecione o runtime de destino na lista suspensa **Runtime de Destino**. O padrão é `win-x86`.
   * Se você precisar remover arquivos adicionais após a implantação, abra as **Opções de Publicação do Arquivo** e marque a caixa de seleção para remover arquivos adicionais no destino.
   * Selecione **Salvar**.
1. Crie um novo site ou atualize um site existente seguindo as solicitações restantes do assistente de publicação.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. No arquivo de projeto, especifique um ou mais [RIDs (identificadores de runtime)](/dotnet/core/rid-catalog). Use `<RuntimeIdentifier>` (singular) para um único RID ou use `<RuntimeIdentifiers>` (plural) para fornecer uma lista de RIDs delimitada por ponto e vírgula. No exemplo a seguir, o RID `win-x86` é especificado:

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. Em um shell de comando, publique o aplicativo na configuração de Versão do runtime do host com o comando [dotnet publish](/dotnet/core/tools/dotnet-publish). No exemplo a seguir, o aplicativo é publicado para o RID `win-x86`. O RID fornecido para a opção `--runtime` precisa ser fornecida na propriedade `<RuntimeIdentifier>` (ou `<RuntimeIdentifiers>`) no arquivo de projeto.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. Mova o conteúdo do diretório *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* para o site no Serviço de Aplicativo. Se for arrastar o conteúdo da pasta *publish* de seu disco rígido local ou do compartilhamento de rede diretamente para o Serviço de Aplicativo no console do Kudu, arraste os arquivos para a pasta `D:\home\site\wwwroot` no console do Kudu.

---

## <a name="protocol-settings-https"></a>Configurações de protocolo (HTTPS)

As associações de protocolo de segurança permitem que você especifique um certificado a ser usado ao responder a solicitações em HTTPS. A associação requer um certificado privado válido ( *.pfx*) emitido para o nome do host específico. Para obter mais informações, consulte [tutorial: associar um certificado SSL personalizado existente ao serviço de Azure app](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Transformação do web.config

Se você precisar transformar o *Web.config* em publicação (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), consulte <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="additional-resources"></a>Recursos adicionais

* [Visão geral do Serviço de Aplicativo](/azure/app-service/app-service-web-overview)
* [Serviço de Aplicativo do Azure: o melhor lugar para hospedar seus aplicativos .NET (vídeo de visão geral com 55 minutos)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: experiência de diagnóstico e solução de problemas do Serviço de Aplicativo do Azure (vídeo com 12 minutos)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

O Serviço de Aplicativo do Azure no Windows Server usa o [IIS (Serviços de Informações da Internet)](https://www.iis.net/). Os tópicos a seguir estão relacionados com a tecnologia subjacente do IIS:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server – conteúdo do administrador de TI para versões atuais e anteriores](/windows-server/windows-server-versions)
