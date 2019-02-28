---
title: Implantar aplicativos ASP.NET Core no Serviço de Aplicativo do Azure
author: guardrex
description: Este artigo contém links para o host do Azure e para implantar recursos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: host-and-deploy/azure-apps/index
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Implantar aplicativos ASP.NET Core no Serviço de Aplicativo do Azure

[Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) é um [serviço de plataforma de computação em nuvem da Microsoft](https://azure.microsoft.com/) para hospedar aplicativos Web, incluindo o ASP.NET Core.

## <a name="useful-resources"></a>Recursos úteis

A [Documentação de Aplicativos Web](/azure/app-service/) do Azure é a página inicial para documentação de Azure Apps, tutoriais, exemplos, guias de instruções e outros recursos. Dois tutoriais importantes que pertencem à hospedagem de aplicativos ASP.NET Core são:

[Criar um aplicativo Web ASP.NET Core no Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Use o Visual Studio para criar e implantar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure no Windows.

[Criar um aplicativo ASP.NET Core no Serviço de Aplicativo no Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Use a linha de comando do Visual Studio para criar e implantar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure no Linux.

Os artigos a seguir estão disponíveis na documentação do ASP.NET Core:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Saiba como criar um aplicativo Web ASP.NET Core usando o Visual Studio e implantá-lo no Serviço de Aplicativo do Azure, usando o Git para implantação contínua.

[Criar seu primeiro pipeline com o Azure Pipelines](/azure/devops/pipelines/get-started-yaml)  
Configurar um build de CI para um aplicativo ASP.NET Core e, em seguida, criar uma versão de implantação contínua para o Serviço de Aplicativo do Azure.

[Área restrita de aplicativo Web do Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Descubra as limitações de tempo de execução do Serviço de Aplicativo do Azure impostas pela plataforma de Aplicativos do Azure.

## <a name="application-configuration"></a>Configuração do aplicativo

### <a name="platform"></a>Plataforma

::: moniker range=">= aspnetcore-2.2"

Os tempos de execução para aplicativos de 32 bits (x86) e 64 bits (x64) estão presentes no Serviço de Aplicativo do Azure. O [SDK do .NET Core](/dotnet/core/sdk) disponível no Serviço de Aplicativo do Azure é de 32 bits, mas você pode implantar aplicativos de 64 bits usando o console do [Kudu](https://github.com/projectkudu/kudu/wiki) ou o [MSDeploy com um perfil de publicação do Visual Studio ou um comando da CLI](xref:host-and-deploy/visual-studio-publish-profiles).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Para aplicativos com dependências nativas, os tempos de execução para aplicativos de 32 bits (x86) estão presentes no Serviço de Aplicativo do Azure. O [SDK do .NET Core](/dotnet/core/sdk) disponível no Serviço de Aplicativo é de 32 bits.

::: moniker-end

### <a name="packages"></a>Pacotes

Incluem os seguintes pacotes NuGet para fornecer recursos de registro automático em log para aplicativos implantados no Serviço de Aplicativo do Azure:

* O [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) usa [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) para fornecer integração leve do ASP.NET Core com o Serviço de Aplicativo do Azure. Os recursos de registro em log adicionais são fornecidos pelo pacote `Microsoft.AspNetCore.AzureAppServicesIntegration`.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executa [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) para adicionar provedores de log de diagnósticos do Serviço de Aplicativo do Azure no pacote `Microsoft.Extensions.Logging.AzureAppServices`.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) fornece implementações de agente para dar suporte a recursos de streaming de log e logs de diagnóstico do Serviço de Aplicativo do Azure.

Os pacotes anteriores não estão disponíveis no [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app). Os aplicativos que são direcionados ao .NET Framework ou fazem referência a um metapacote `Microsoft.AspNetCore.App` precisam referenciar explicitamente os pacotes individuais no arquivo de projeto do aplicativo.

## <a name="override-app-configuration-using-the-azure-portal"></a>Substituir a configuração do aplicativo no Portal do Azure

As configurações do aplicativo no portal do Azure permitem definir variáveis de ambiente para o aplicativo. As variáveis de ambiente podem ser consumidas pelo [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Quando uma configuração de aplicativo é criada ou modificada no Portal do Azure e o botão **Salvar** é selecionado, o Aplicativo Azure é reiniciado. A variável de ambiente estará disponível para o aplicativo após o serviço ser reiniciado.

Quando o aplicativo usa o [host da Web](xref:fundamentals/host/web-host) e cria o host usando [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), as variáveis de ambiente que configuram o host usam o prefixo `ASPNETCORE_`. Para saber mais, confira <xref:fundamentals/host/web-host> e o [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Quando o aplicativo usa o [host genérico](xref:fundamentals/host/generic-host), as variáveis de ambiente não são carregadas na configuração do aplicativo por padrão, e o provedor de configuração deve ser adicionado pelo desenvolvedor. O desenvolvedor determina o prefixo da variável de ambiente quando o provedor de configuração é adicionado. Para saber mais, confira <xref:fundamentals/host/generic-host> e o [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

O Middleware de integração do IIS, que configura Middleware de cabeçalhos encaminhados, e o módulo do ASP.NET Core são configurados para encaminhar o esquema (HTTP/HTTPS) e o endereço IP remoto de onde a solicitação foi originada. Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga. Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Monitoramento e registro em log

Os aplicativos ASP.NET Core implantados no Serviço de Aplicativo recebem automaticamente uma extensão do Serviço de Aplicativo, **Extensões de Log do ASP.NET Core**. A extensão habilita o registro em log do Azure.

Para monitoramento, registro em log e informações de solução de problemas, veja os seguintes artigos:

[Como: monitorar aplicativos no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-monitor)  
Saiba como examinar as cotas e métricas para aplicativos e planos do Serviço de Aplicativo.

[Habilitar log de diagnósticos para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log)  
Descubra como habilitar e acessar o log de diagnósticos para os códigos de status HTTP, solicitações com falha e atividade do servidor Web.

<xref:fundamentals/error-handling>  
Entenda as abordagens comuns para o tratamento de erros em aplicativos ASP.NET Core.

<xref:host-and-deploy/azure-apps/troubleshoot>  
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

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>Implantar a versão de visualização do ASP.NET Core para o Serviço de Aplicativo do Azure

Use uma das abordagens a seguir:

* [Instalar a extensão de site da versão prévia](#install-the-preview-site-extension).
* [Implantar o aplicativo autocontido](#deploy-the-app-self-contained).
* [Usar o Docker com aplicativos Web para contêineres](#use-docker-with-web-apps-for-containers).

### <a name="install-the-preview-site-extension"></a>Instalar a extensão de site de visualização

Se houver problemas ao usar a extensão de site de visualização, abra um problema no [GitHub](https://github.com/aspnet/azureintegration/issues/new).

1. No portal do Azure, navegue até o Serviço de Aplicativo.
1. Selecione o aplicativo Web.
1. Insira "ex" na caixa de pesquisa para filtrar por "Extensões" ou role para baixo na lista de ferramentas de gerenciamento.
1. Selecione **Extensões**.
1. Selecione **Adicionar**.
1. Selecione a extensão **Tempo de execução do ASP.NET Core {X.Y} ({x64|x86})** na lista, em que `{X.Y}` é a versão prévia do ASP.NET Core e `{x64|x86}` especifica a plataforma.
1. Selecione **OK** para aceitar os termos legais.
1. Selecione **OK** para instalar a extensão.

Quando a operação for concluída, a versão prévia mais recente do .NET Core será instalada. Verifique a instalação:

1. Selecione **Ferramentas Avançadas**.
1. Selecione **Acessar** em **Ferramentas Avançadas**.
1. Selecione o item de menu **Console de depuração** > **PowerShell**.
1. No prompt do PowerShell, execute o seguinte comando. Substitua a versão do tempo de execução do ASP.NET Core por `{X.Y}` e a plataforma por `{PLATFORM}` no comando:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```
   O comando retornará `True` quando o tempo de execução da versão prévia x64 estiver instalado.

> [!NOTE]
> A arquitetura da plataforma (x86/x64) de um aplicativo dos Serviços de Aplicativos é definida nas configurações do aplicativo no portal do Azure para aplicativos hospedados em um nível de hospedagem de computação da série A ou melhor. Se o aplicativo for executado no modo em processo e a arquitetura da plataforma estiver configurada para 64 bits (x64), o Módulo do ASP.NET Core usará o tempo de execução da versão prévia de 64 bits, se estiver presente. Instale a extensão **Tempo de execução do ASP.NET Core {X.Y} (x64)**.
>
> Depois de instalar o tempo de execução da versão prévia x64, execute o seguinte comando na janela de comando do Kudu PowerShell para verificar a instalação. Substitua a versão de tempo de execução do ASP.NET Core por `{X.Y}` no comando:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
> O comando retornará `True` quando o tempo de execução da versão prévia x64 estiver instalado.

> [!NOTE]
> As **Extensões do ASP.NET Core** habilitam uma funcionalidade adicional para o ASP.NET Core nos Serviços de Aplicativo do Azure, como a habilitação do registro em log do Azure. A extensão é instalada automaticamente durante a implantação do Visual Studio. Se a extensão não estiver instalada, instale-a para o aplicativo.

**Usar a extensão de site de visualização com um modelo do ARM**

Se um modelo do ARM for usado para criar e implantar aplicativos, o tipo de recurso `siteextensions` poderá ser usado para adicionar a extensão de site a um aplicativo Web. Por exemplo:

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-the-app-self-contained"></a>Implantar o aplicativo autocontido

Uma [SCD (implantação autocontida)](/dotnet/core/deploying/#self-contained-deployments-scd) voltada para um tempo de execução de versão prévia transporta o tempo de execução da versão prévia na implantação.

Ao implantar um aplicativo autocontido:

* O site no Serviço de Aplicativo do Azure não exige a [extensão de site da versão prévia](#install-the-preview-site-extension).
* O aplicativo precisa ser publicado após uma abordagem diferente da publicação em uma [FDD (implantação dependente de estrutura)](/dotnet/core/deploying#framework-dependent-deployments-fdd).

#### <a name="publish-from-visual-studio"></a>Publicar no Visual Studio

1. Selecione **Build** > **Publicar {Nome do Aplicativo}** na barra de ferramentas do Visual Studio.
1. Na caixa de diálogo **Escolher um destino de publicação**, confirme se o **Serviço de Aplicativo** está selecionado.
1. Selecione **Avançado**. A caixa de diálogo **Publicar** será aberta.
1. Na caixa de diálogo **Publicar**:
   * Confirme se a configuração **Versão** está selecionada.
   * Abra a lista suspensa **Modo de Implantação** e selecione **Autocontido**.
   * Selecione o tempo de execução de destino na lista suspensa **Tempo de Execução de Destino**. O padrão é `win-x86`.
   * Se você precisar remover arquivos adicionais após a implantação, abra as **Opções de Publicação do Arquivo** e marque a caixa de seleção para remover arquivos adicionais no destino.
   * Selecione **Salvar**.
1. Crie um novo site ou atualize um site existente seguindo as solicitações restantes do assistente de publicação.

#### <a name="publish-using-command-line-interface-cli-tools"></a>Publicar usando as ferramentas de CLI (interface de linha de comando)

1. No arquivo de projeto, especifique um ou mais [RIDs (identificadores de tempo de execução)](/dotnet/core/rid-catalog). Use `<RuntimeIdentifier>` (singular) para um único RID ou use `<RuntimeIdentifiers>` (plural) para fornecer uma lista de RIDs delimitada por ponto e vírgula. No exemplo a seguir, o RID `win-x86` é especificado:

   ```xml
   <PropertyGroup>
     <TargetFramework>netcoreapp2.1</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```
1. Em um shell de comando, publique o aplicativo na configuração de Versão do tempo de execução do host com o comando [dotnet publish](/dotnet/core/tools/dotnet-publish). No exemplo a seguir, o aplicativo é publicado para o RID `win-x86`. O RID fornecido para a opção `--runtime` precisa ser fornecida na propriedade `<RuntimeIdentifier>` (ou `<RuntimeIdentifiers>`) no arquivo de projeto.

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```
1. Mova o conteúdo do diretório *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* para o site no Serviço de Aplicativo.

### <a name="use-docker-with-web-apps-for-containers"></a>Usar o Docker com aplicativos Web para contêineres

O [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contém as imagens de versão prévia do Docker mais recentes. As imagens podem ser usadas como uma imagem de base. Use a imagem e implante aplicativos Web para contêineres normalmente.

## <a name="protocol-settings-https"></a>Configurações de protocolo (HTTPS)

As associações de protocolo de segurança permitem que você especifique um certificado a ser usado ao responder a solicitações em HTTPS. A associação requer um certificado privado válido (*.pfx*) emitido para o nome do host específico. Para obter mais informações, confira [Tutorial: vincular um certificado SSL personalizado ao serviço Aplicativos Web do Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Transformação do Web.config

Se você precisar transformar o *Web.config* em publicação (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), consulte <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="additional-resources"></a>Recursos adicionais

* [Visão geral de aplicativos Web (vídeo de visão geral com 5 minutos)](/azure/app-service/app-service-web-overview)
* [Serviço de Aplicativo do Azure: o melhor lugar para hospedar seus aplicativos .NET (vídeo de visão geral com 55 minutos)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: experiência de diagnóstico e solução de problemas do Serviço de Aplicativo do Azure (vídeo com 12 minutos)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

O Serviço de Aplicativo do Azure no Windows Server usa o [IIS (Serviços de Informações da Internet)](https://www.iis.net/). Os tópicos a seguir estão relacionados com a tecnologia subjacente do IIS:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Biblioteca do Microsoft TechNet: Windows Server](/windows-server/windows-server-versions)
