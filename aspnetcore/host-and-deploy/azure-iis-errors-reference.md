---
title: Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS com o ASP.NET Core
author: guardrex
description: Obtenha conselhos de solução de problemas para erros comuns ao hospedar aplicativos ASP.NET Core no Serviço de Aplicativos do Azure e no IIS.
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 976f7e3fbeab9e81ba99e2dd7d09a892b854651b
ms.sourcegitcommit: 3c2ba9a0d833d2a096d9d800ba67a1a7f9491af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854455"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS com o ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Este tópico oferece conselhos de solução de problemas para erros comuns ao hospedar aplicativos ASP.NET Core no Serviço de Aplicativos do Azure e no IIS.

Colete as seguintes informações:

* Comportamento do navegador (código de status e mensagem de erro)
* Entradas do Log de Eventos do Aplicativo
  * Serviço de Aplicativo do Azure &ndash; Confira <xref:host-and-deploy/azure-apps/troubleshoot>.
  * IIS
    1. Selecione **Iniciar** no menu **Windows**, digite *Visualizador de Eventos* e pressione **Enter**.
    1. Após o **Visualizador de Eventos** ser aberto, expanda **Logs do Windows** > **Aplicativo** na barra lateral.
* Entradas do log de depuração e stdout do Módulo do ASP.NET Core
  * Serviço de Aplicativo do Azure &ndash; Confira <xref:host-and-deploy/azure-apps/troubleshoot>.
  * IIS &ndash; Siga as instruções nas seções [Criação de log e redirecionamento](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) e [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do tópico Módulo do ASP.NET Core.

Compare as informações do erro para os erros comuns a seguir. Se uma correspondência for encontrada, siga o aviso de solução de problemas.

A lista de erros neste tópico não é exaustiva. Se você encontrar um erro não listado aqui, abra um novo problema usando o botão **Comentários sobre o Conteúdo** na parte inferior deste tópico com instruções detalhadas sobre como reproduzir o erro.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a>O instalador não pode obter os Pacotes Redistribuíveis do VC++

* **Exceção do instalador:** 0x80072efd **–OU–** 0x80072f76 – erro não especificado

* **Exceção do log do instalador&#8224;:** Erro 0x80072efd **–OU–** 0x80072f76: Falha ao executar o pacote EXE

  &#8224;O log está localizado em *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.

Solução de problemas:

Se o sistema não tiver acesso à Internet durante a [instalação do pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), essa exceção ocorrerá quando o instalador for impedido de obter os *Pacotes Redistribuíveis do Microsoft Visual C++ 2015*. Obtenha um instalador do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=53840). Se o instalador falhar, o servidor poderá não receber o tempo de execução do .NET Core necessário para hospedar uma [FDD (implantação dependente de estrutura)](/dotnet/core/deploying/#framework-dependent-deployments-fdd). Se estiver hospedando uma FDD, confirme se o tempo de execução está instalado em **Programas e Recursos** ou **Aplicativos e recursos**. Se um tempo de execução específico for necessário, baixe o tempo de execução dos [Arquivos de Download do .NET](https://dotnet.microsoft.com/download/archives) e instale-o no sistema. Depois de instalar o tempo de execução, reinicie o sistema ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>O upgrade do sistema operacional removeu o Módulo do ASP.NET Core de 32 bits

**Log do Aplicativo:** A DLL do Módulo **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** falhou ao ser carregada. Os dados são o erro.

Solução de problemas:

Arquivos que não são do sistema operacional no diretório **C:\Windows\SysWOW64\inetsrv** não são preservados durante um upgrade do sistema operacional. Se o Módulo do ASP.NET Core estiver instalado antes de uma atualização do sistema operacional e, em seguida, qualquer pool de aplicativos for executado no modo de 32 bits após uma atualização do sistema operacional, esse problema será encontrado. Após um upgrade do sistema operacional, repare o Módulo do ASP.NET Core. Veja [Instalar o pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Selecione **Reparar** ao executar o instalador.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Um aplicativo x86 é implantado, mas o pool de aplicativos não está habilitado para aplicativos de 32 bits

* **Navegador:** Erro HTTP 500.30 – Falha de início no processo do ANCM

* **Log do Aplicativo:** Aplicativo '/LM/W3SVC/5/ROOT' com raiz física '{PATH}' atingiu uma exceção gerenciada inesperada, código de exceção = '0xe0434352'. Verifique os logs de stderr para obter mais informações. Aplicativo '/LM/W3SVC/5/ROOT' com raiz física '{PATH}' falhou ao carregar o clr e o aplicativo gerenciado. O thread de trabalho do CLR foi encerrado prematuramente

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio.

::: moniker range=">= aspnetcore-2.2"

* **Log de depuração do módulo do ASP.NET Core:** HRESULT com falha retornou: 0x8007023e

::: moniker-end

Esse cenário é interceptado pelo SDK ao publicar um aplicativo autocontido. O SDK produzirá um erro se o RID não coincidir com o destino da plataforma (por exemplo, RID `win10-x64` com `<PlatformTarget>x86</PlatformTarget>` no arquivo de projeto).

Solução de problemas:

Para uma implantação dependente da estrutura x86 (`<PlatformTarget>x86</PlatformTarget>`), habilite o pool de aplicativos de IIS para aplicativos de 32 bits. No Gerenciador do IIS, abra as **Configurações Avançadas** do pool de aplicativos e defina **Habilitar Aplicativos de 32 Bits** como **Verdadeiro**.

## <a name="platform-conflicts-with-rid"></a>Conflitos de plataforma com o RID

* **Navegador:** Erro HTTP 502.5 – falha do processo

* **Log do Aplicativo:** O aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' falhou ao iniciar o processo com a linha de comando '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.

* **Log de stdout do Módulo do ASP.NET Core:** Exceção sem tratamento: System.BadImageFormatException: Não foi possível carregar arquivo ou o assembly '{ASSEMBLY}.dll'. Foi feita uma tentativa de carregar um programa com um formato incorreto.

Solução de problemas:

* Confirme se o aplicativo é executado localmente no Kestrel. Uma falha do processo pode ser o resultado de um problema no aplicativo. Para obter mais informações, confira [Solução de problemas (IIS)](xref:host-and-deploy/iis/troubleshoot) ou [Solução de problemas (Serviço de Aplicativo do Azure)](xref:host-and-deploy/azure-apps/troubleshoot).

* Se essa exceção ocorrer para uma implantação dos Aplicativos do Azure ao fazer upgrade de um aplicativo e implantar assemblies mais recentes, exclua manualmente todos os arquivos da implantação anterior. Assemblies incompatíveis remanescentes podem resultar em uma exceção `System.BadImageFormatException` durante a implantação de um aplicativo atualizado.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Ponto de extremidade de URI incorreto ou site interrompido

* **Navegador:** ERR_CONNECTION_REFUSED **–OU–** Não é possível estabelecer conexão

* **Log do Aplicativo:** Nenhuma entrada

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.

::: moniker range=">= aspnetcore-2.2"

* **Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.

::: moniker-end

Solução de problemas:

* Confirme se o ponto de extremidade do URI correto para o aplicativo está sendo usado. Verifique as associações.

* Confirme que o site do IIS não está no estado *Parado*.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Recursos do servidor CoreWebEngine ou W3SVC desabilitados

**Exceção do Sistema Operacional:** Os recursos CoreWebEngine e W3SVC do IIS 7.0 devem ser instalados para usar o Módulo do ASP.NET Core.

Solução de problemas:

Confirme que a função e os recursos apropriados estão habilitados. Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Caminho físico do site incorreto ou aplicativo ausente

* **Navegador:** 403 Proibido – acesso negado **–OU–** 403.14 Proibido – o servidor Web está configurado para não listar o conteúdo deste diretório.

* **Log do Aplicativo:** Nenhuma entrada

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.

::: moniker range=">= aspnetcore-2.2"

* **Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.

::: moniker-end

Solução de problemas:

Confira as **Configurações Básicas** no site do IIS e a pasta do aplicativo físico. Confirme que o aplicativo está na pasta no **Caminho físico** do site do IIS.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Função incorreta, Módulo do ASP.NET Core Não Instalado ou permissões incorretas

* **Navegador:** 500.19 Erro interno do servidor – a página solicitada não pode ser acessada porque os dados de configuração relacionados da página são inválidos. **–OU–** Esta página não pode ser exibida

* **Log do Aplicativo:** Nenhuma entrada

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.

::: moniker range=">= aspnetcore-2.2"

* **Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.

::: moniker-end

Solução de problemas:

* Confirme que você habilitou a função apropriada. Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Abra **Programas e Recursos** ou **Aplicativos e Recursos** e confirme se a **Hospedagem do Windows Server** está instalada. Se a **Hospedagem do Windows Server** não estiver presente na lista de programas instalados, baixe e instale o Pacote de Hospedagem do .NET Core.

  [Instalador de pacote de hospedagem do .NET Core atual (download direto)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Verifique se o **Pool de aplicativos** > **Modelo de processo** > **Identidade** está definido como **ApplicationPoolIdentity** ou se a identidade personalizada tem as permissões corretas para acessar a pasta de implantação do aplicativo.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>processPath incorreto, variável de PATH ausente, pacote de hospedagem não instalado, sistema/IIS não reiniciado, Pacotes Redistribuíveis do VC++ não instalados ou violação de acesso de dotnet.exe

::: moniker range=">= aspnetcore-2.2"

* **Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM

* **Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' falhou ao iniciar o processo com a linha de comando '"{...}" ', ErrorCode = '0x80070002 : 0. Não foi possível iniciar o aplicativo '{PATH}'. O executável não foi encontrado em '{PATH}'. Falha ao iniciar o aplicativo '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.

* **Log de depuração do módulo do ASP.NET Core:** Log de eventos: Não foi possível iniciar o aplicativo '{PATH}'. O executável não foi encontrado em '{PATH}'. HRESULT com falha retornou: 0x8007023e

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Navegador:** Erro HTTP 502.5 – falha do processo

* **Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' falhou ao iniciar o processo com a linha de comando '"{...}" ', ErrorCode = '0x80070002 : 0.

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio.

::: moniker-end

Solução de problemas:

* Confirme se o aplicativo é executado localmente no Kestrel. Uma falha do processo pode ser o resultado de um problema no aplicativo. Para obter mais informações, confira [Solução de problemas (IIS)](xref:host-and-deploy/iis/troubleshoot) ou [Solução de problemas (Serviço de Aplicativo do Azure)](xref:host-and-deploy/azure-apps/troubleshoot).

* Verifique o atributo *processPath* no elemento `<aspNetCore>` em *web.config* para confirmar se ele é `dotnet` para uma FDD (implantação dependente de estrutura) ou `.\{ASSEMBLY}.exe` para uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd).

* Para uma FDD, o *dotnet.exe* pode não estar acessível por meio das configurações de PATH. Confirme se *C:\Arquivos de Programas\dotnet\\* existe nas configurações de PATH do Sistema.

* Para uma FDD, o *dotnet.exe* pode não estar acessível para a identidade do usuário do pool de aplicativos. Confirme se a identidade do usuário do pool de aplicativos tem acesso ao diretório *C:\Arquivos de Programas\dotnet*. Confirme se não há nenhuma regra de negação configurada para a identidade do usuário do pool de aplicativos no *C:\Arquivos de Programas\dotnet* e nos diretórios do aplicativo.

* Talvez você tenha implantado uma FDD e instalado o .NET Core sem reiniciar o IIS. Reinicie o servidor ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.

* Você pode ter implantado uma FDD sem instalar o tempo de execução do .NET Core no sistema de hospedagem. Se o tempo de execução do .NET Core ainda não foi instalado, execute o **Instalador do Pacote de Hospedagem do .NET Core** no sistema.

  [Instalador de pacote de hospedagem do .NET Core atual (download direto)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Se um tempo de execução específico for necessário, baixe o tempo de execução dos [Arquivos de Download do .NET](https://dotnet.microsoft.com/download/archives) e instale-o no sistema. Conclua a instalação reiniciando o sistema ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.

* Talvez você tenha implantado uma FDD e os *Pacotes redistribuíveis do Microsoft Visual C++ 2015 (x64)* não estejam instalados no sistema. Obtenha um instalador do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=53840).

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Argumentos incorretos do elemento \<aspNetCore>

::: moniker range=">= aspnetcore-2.2"

* **Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM

* **Log do Aplicativo:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa. Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador. Não foi possível localizar o manipulador de solicitação inprocess. Saída capturada da invocação do hostfxr: Você quis dizer executar comandos do SDK do dotnet? Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Falha ao iniciar o aplicativo '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.

* **Log de stdout do Módulo do ASP.NET Core:** Você quis dizer executar comandos do SDK do dotnet? Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **Log de depuração do módulo do ASP.NET Core:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa. Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador. HRESULT com falha retornou: 0x8000ffff Não foi possível localizar o manipulador de solicitação inprocess. Saída capturada da invocação do hostfxr: Você quis dizer executar comandos do SDK do dotnet? Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT com falha retornou: 0x8000ffff

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Navegador:** Erro HTTP 502.5 – falha do processo

* **Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com a raiz física 'C:\{PATH}\' falha ao iniciar o processo com a linha de comando '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005: 80008081.

* **Log de stdout do Módulo do ASP.NET Core:** O aplicativo a ser executado não existe: 'PATH\{ASSEMBLY}.dll'

::: moniker-end

Solução de problemas:

* Confirme se o aplicativo é executado localmente no Kestrel. Uma falha do processo pode ser o resultado de um problema no aplicativo. Para obter mais informações, confira [Solução de problemas (IIS)](xref:host-and-deploy/iis/troubleshoot) ou [Solução de problemas (Serviço de Aplicativo do Azure)](xref:host-and-deploy/azure-apps/troubleshoot).

* Examine o atributo *arguments* no elemento `<aspNetCore>` no *web.config* para confirmar se ele: (a) é `.\{ASSEMBLY}.dll` de uma FDD (implantação dependente de estrutura); ou (b) não está presente, é uma cadeia de caracteres vazia (`arguments=""`) ou uma lista de argumentos do aplicativo (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) para uma SCD (implantação autossuficiente).

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a>Estrutura compartilhada do .NET Core ausente

* **Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM

* **Log do Aplicativo:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa. Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador. Não foi possível localizar o manipulador de solicitação inprocess. Saída capturada da invocação do hostfxr: Não foi possível encontrar nenhuma versão de estrutura compatível. A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}', não foi encontrada.

Falha ao iniciar o aplicativo '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.

* **Log de stdout do Módulo do ASP.NET Core:** Não foi possível encontrar nenhuma versão de estrutura compatível. A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}', não foi encontrada.

* **Log de depuração do módulo do ASP.NET Core:** HRESULT com falha retornou: 0x8000ffff

::: moniker-end

Solução de problemas:

Para uma FDD (implantação dependente de estrutura), confirme se você tem o tempo de execução correto instalado no sistema.

## <a name="stopped-application-pool"></a>Pool de aplicativos interrompido

* **Navegador:** 503 Serviço Não Disponível

* **Log do Aplicativo:** Nenhuma entrada

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.

::: moniker range=">= aspnetcore-2.2"

* **Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.

::: moniker-end

Solução de problemas:

Confirme que o Pool de Aplicativos não está no estado *Parado*.

## <a name="sub-application-includes-a-handlers-section"></a>O subaplicativo inclui uma seção \<manipuladores>

* **Navegador:** Erro HTTP 500.19 – Erro Interno do Servidor

* **Log do Aplicativo:** Nenhuma entrada

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log do aplicativo raiz é criado e mostra uma operação normal. O arquivo de log do subaplicativo não é criado.

::: moniker range=">= aspnetcore-2.2"

* **Log de depuração do módulo do ASP.NET Core:** O arquivo de log do aplicativo raiz é criado e mostra uma operação normal. O arquivo de log do subaplicativo não é criado.

::: moniker-end

Solução de problemas:

::: moniker range=">= aspnetcore-2.2"

Confirme se o arquivo *web.config* do subaplicativo não inclui uma seção `<handlers>` ou que o subaplicativo não herda os manipuladores do aplicativo pai.

A seção `<system.webServer>` do aplicativo pai de *web.config* é colocada dentro de um elemento `<location>`. A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo pai. Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Confirme se o arquivo *web.config* do subaplicativo não inclui uma seção `<handlers>`.

::: moniker-end

## <a name="stdout-log-path-incorrect"></a>caminho do log de stdout incorreto

* **Navegador:** O aplicativo responde normalmente.

::: moniker range=">= aspnetcore-2.2"

* **Log do Aplicativo:** Não foi possível iniciar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Mensagem de exceção: HRESULT 0x80070005 retornado em {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84. Não foi possível parar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Mensagem de exceção: HRESULT 0x80070002 retornado em {PATH}. Não foi possível iniciar o redirecionamento de stdout em {PATH}\aspnetcorev2_inprocess.dll.

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.

* **Log de depuração do módulo do ASP.NET Core:** Não foi possível iniciar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Mensagem de exceção: HRESULT 0x80070005 retornado em {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84. Não foi possível parar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Mensagem de exceção: HRESULT 0x80070002 retornado em {PATH}. Não foi possível iniciar o redirecionamento de stdout em {PATH}\aspnetcorev2_inprocess.dll.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Log do Aplicativo:** Aviso: Não foi possível criar stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.

::: moniker-end

Solução de problemas:

* O caminho `stdoutLogFile` especificado no elemento `<aspNetCore>` de *web.config* não existe. Para obter mais informações, confira [Módulo ASP.NET Core: criação de log e redirecionamento](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* O usuário do pool de aplicativos não tem acesso de gravação para o caminho do log de stdout.

## <a name="application-configuration-general-issue"></a>Problema geral de configuração do aplicativo

::: moniker range=">= aspnetcore-2.2"

* **Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM **–OU–** Erro HTTP 500.30 – Falha de início no processo do ANCM

* **Log do Aplicativo:** Variável

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio, ou criado com entradas normais até o ponto da falha do aplicativo.

* **Log de depuração do módulo do ASP.NET Core:** Variável

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Navegador:** Erro HTTP 502.5 – falha do processo

* **Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' criada no processo com a linha de comando '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ', mas falhou, não respondeu ou não escutou na porta '{PORT}' fornecida, ErrorCode = '{ERROR CODE}'

* **Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio.

::: moniker-end

Solução de problemas:

O processo não pôde ser iniciado, provavelmente, devido a um problema de programação ou configuração do aplicativo.

Para mais informações, consulte os seguintes tópicos:

* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:test/troubleshoot>
