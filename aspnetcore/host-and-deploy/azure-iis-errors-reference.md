---
title: Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS com o ASP.NET Core
author: rick-anderson
description: Obtenha conselhos de solução de problemas para erros comuns ao hospedar aplicativos ASP.NET Core no Serviço de Aplicativos do Azure e no IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 635c4cf6f12e62ca7e795b3b3b47e9445b945551
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511594"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS com o ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

Este tópico descreve os erros comuns e fornece conselhos de solução de problemas para erros específicos ao hospedar ASP.NET Core aplicativos no serviço de aplicativos do Azure e no IIS.

Para obter diretrizes gerais de solução de problemas, consulte <xref:test/troubleshoot-azure-iis>.

Colete as seguintes informações:

* Comportamento do navegador (código de status e mensagem de erro)
* Entradas do Log de Eventos do Aplicativo
  * Serviço de Aplicativo do Azure &ndash; Confira <xref:test/troubleshoot-azure-iis>.
  * IIS
    1. Selecione **Iniciar** no menu **Windows**, digite *Visualizador de Eventos* e pressione **Enter**.
    1. Depois que o **Visualizador de eventos** for aberto, expanda **Logs do Windows** > **aplicativo** na barra lateral.
* Entradas do log de depuração e stdout do Módulo do ASP.NET Core
  * Serviço de Aplicativo do Azure &ndash; Confira <xref:test/troubleshoot-azure-iis>.
  * IIS &ndash; Siga as instruções nas seções [Criação de log e redirecionamento](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) e [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do tópico Módulo do ASP.NET Core.

Compare as informações do erro para os erros comuns a seguir. Se uma correspondência for encontrada, siga o aviso de solução de problemas.

A lista de erros neste tópico não é exaustiva. Se você encontrar um erro não listado aqui, abra um novo problema usando o botão **Comentários sobre o Conteúdo** na parte inferior deste tópico com instruções detalhadas sobre como reproduzir o erro.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>O upgrade do sistema operacional removeu o Módulo do ASP.NET Core de 32 bits

**Log do Aplicativo:** a DLL do Módulo **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** falhou ao ser carregada. Os dados são o erro.

Solucionando problemas:

Arquivos que não são do sistema operacional no diretório **C:\Windows\SysWOW64\inetsrv** não são preservados durante um upgrade do sistema operacional. Se o Módulo do ASP.NET Core estiver instalado antes de uma atualização do sistema operacional e, em seguida, qualquer pool de aplicativos for executado no modo de 32 bits após uma atualização do sistema operacional, esse problema será encontrado. Após um upgrade do sistema operacional, repare o Módulo do ASP.NET Core. Veja [Instalar o pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Selecione **Reparar** ao executar o instalador.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Extensão de site ausente, extensões de site de 32 bits (x86) e 64 bits (x64) instaladas ou conjunto de bits incorreto do processo

*Aplica-se aos aplicativos hospedados pelos Serviços de Aplicativo do Azure.*

* **Navegador:** Erro HTTP 500,0-ANCM falha no carregamento do manipulador em processo

* **Log do aplicativo:** Invocar hostfxr para localizar o manipulador de solicitação de inprocesso falhou sem encontrar nenhuma dependência nativa. Não foi possível localizar o manipulador de solicitação inprocess. Saída capturada de invocação de hostfxr: não foi possível encontrar nenhuma versão de estrutura compatível. A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada. Falha ao iniciar o aplicativo '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.

* **Log de stdout do módulo ASP.NET Core:** Não foi possível encontrar nenhuma versão de estrutura compatível. A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada.

* **Log de depuração do módulo ASP.NET Core:** Invocar hostfxr para localizar o manipulador de solicitação de inprocesso falhou sem encontrar nenhuma dependência nativa. Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador. Falha de HRESULT retornado: 0x8000ffff. Não foi possível localizar o manipulador de solicitação inprocess. Não foi possível encontrar nenhuma versão de estrutura compatível. A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada.

Solucionando problemas:

* Se estiver executando o aplicativo em um runtime de visualização, instale a extensão de site de 32 bits (x86) **ou** de 64 bits (x64) que corresponda ao número de bit do aplicativo e à versão de runtime do aplicativo. **Não instale extensões ou várias versões de runtime da extensão.**

  * Tempo de execução do ASP.NET Core {RUNTIME VERSION} (x86)
  * Tempo de execução do ASP.NET Core {RUNTIME VERSION} (x64)

  Reinicie o aplicativo. Aguarde vários segundos até que o aplicativo seja reiniciado.

* Se a execução do aplicativo em um runtime de visualização e as [extensões de site](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) de 32 bits (x86) e 64 bits (x64) estiverem instaladas, desinstale a extensão de site que não corresponde ao número de bit do aplicativo. Depois de remover a extensão de site, reinicie o aplicativo. Aguarde vários segundos até que o aplicativo seja reiniciado.

* Se executar o aplicativo em um runtime de visualização e o número de bit da extensão de site corresponder ao do aplicativo, confirme se a *versão do runtime* da extensão de site de visualização corresponde à versão do runtime do aplicativo.

* Confirme se a **Plataforma** do aplicativo em **Configurações do aplicativo** corresponde ao número de bit do aplicativo.

Para obter mais informações, consulte <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Um aplicativo x86 é implantado, mas o pool de aplicativos não está habilitado para aplicativos de 32 bits

* **Navegador:** Erro HTTP 500,30-ANCM falha no início do processo

* **Log do aplicativo:** O aplicativo '/LM/W3SVC/5/ROOT ' com a raiz física ' {PATH} ' atingiu uma exceção gerenciada inesperada, código de exceção = ' 0xe0434352 '. Verifique os logs de stderr para obter mais informações. Aplicativo '/LM/W3SVC/5/ROOT' com raiz física '{PATH}' falhou ao carregar o clr e o aplicativo gerenciado. O thread de trabalho do CLR foi encerrado prematuramente

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log é criado, mas vazio.

* **Log de depuração do módulo ASP.NET Core:** HRESULT com falha retornado: 0x8007023e

Esse cenário é interceptado pelo SDK ao publicar um aplicativo autocontido. O SDK produzirá um erro se o RID não coincidir com o destino da plataforma (por exemplo, RID `win10-x64` com `<PlatformTarget>x86</PlatformTarget>` no arquivo de projeto).

Solucionando problemas:

Para uma implantação dependente da estrutura x86 (`<PlatformTarget>x86</PlatformTarget>`), habilite o pool de aplicativos de IIS para aplicativos de 32 bits. No Gerenciador do IIS, abra as **Configurações Avançadas** do pool de aplicativos e defina **Habilitar Aplicativos de 32 Bits** como **Verdadeiro**.

## <a name="platform-conflicts-with-rid"></a>Conflitos de plataforma com o RID

* **Navegador:** 502.5 Erro HTTP – falha do processo

* **Log do aplicativo:** O aplicativo ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' com a raiz física ' C:\{PATH}\' falhou ao iniciar o processo com linha de comando ' "C:\{PATH} {ASSEMBLY}. {exe | dll} "', ErrorCode = ' 0x80004005: FF.

* **Log de stdout do módulo ASP.NET Core:** Exceção sem tratamento: System. BadImageFormatException: não foi possível carregar o arquivo ou assembly ' {ASSEMBLY}. dll '. Foi feita uma tentativa de carregar um programa com um formato incorreto.

Solucionando problemas:

* Confirme se o aplicativo é executado localmente no Kestrel. Uma falha do processo pode ser o resultado de um problema no aplicativo. Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.

* Se essa exceção ocorrer para uma implantação dos Aplicativos do Azure ao fazer upgrade de um aplicativo e implantar assemblies mais recentes, exclua manualmente todos os arquivos da implantação anterior. Assemblies incompatíveis remanescentes podem resultar em uma exceção `System.BadImageFormatException` durante a implantação de um aplicativo atualizado.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Ponto de extremidade de URI incorreto ou site interrompido

* **Navegador:** ERR_CONNECTION_REFUSED **--ou--** não é possível se conectar

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

* **Log de depuração do módulo ASP.NET Core:** O arquivo de log não foi criado.

Solucionando problemas:

* Confirme se o ponto de extremidade do URI correto para o aplicativo está sendo usado. Verifique as associações.

* Confirme que o site do IIS não está no estado *Parado*.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Recursos do servidor CoreWebEngine ou W3SVC desabilitados

**Exceção do Sistema Operacional:** os recursos CoreWebEngine e W3SVC do IIS 7.0 devem ser instalados para usar o Módulo do ASP.NET Core.

Solucionando problemas:

Confirme que a função e os recursos apropriados estão habilitados. Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Caminho físico do site incorreto ou aplicativo ausente

* **Navegador:** 403 Proibido – acesso negado **OU** 403.14 Proibido – o servidor Web está configurado para não listar o conteúdo deste diretório.

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

* **Log de depuração do módulo ASP.NET Core:** O arquivo de log não foi criado.

Solucionando problemas:

Confira as **Configurações Básicas** no site do IIS e a pasta do aplicativo físico. Confirme que o aplicativo está na pasta no **Caminho físico** do site do IIS.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Função incorreta, Módulo do ASP.NET Core Não Instalado ou permissões incorretas

* **Navegador:** 500.19 Erro interno do servidor – a página solicitada não pode ser acessada porque os dados de configuração relacionados da página são inválidos. **–OU–** Esta página não pode ser exibida

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

* **Log de depuração do módulo ASP.NET Core:** O arquivo de log não foi criado.

Solucionando problemas:

* Confirme que você habilitou a função apropriada. Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Abra **Programas e Recursos** ou **Aplicativos e Recursos** e confirme se a **Hospedagem do Windows Server** está instalada. Se a **Hospedagem do Windows Server** não estiver presente na lista de programas instalados, baixe e instale o Pacote de Hospedagem do .NET Core.

  [Instalador de pacote de hospedagem do .NET Core atual (download direto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Verifique se o **pool de aplicativos** > **modelo de processo** > **identidade** está definido como **ApplicationPoolIdentity** ou se a identidade personalizada tem as permissões corretas para acessar a pasta de implantação do aplicativo.

* Se você desinstalou o Pacote de Hospedagem do ASP.NET Core e instalou uma versão anterior do pacote de hospedagem, o arquivo *applicationHost.config* não inclui uma seção para o Módulo do ASP.NET Core. Abra *applicationHost.config* em *%windir%/System32/inetsrv/config* e encontre o grupo de seção `<configuration><configSections><sectionGroup name="system.webServer">`. Se estiver faltando a seção do Módulo do ASP.NET Core no grupo de seções, adicione o elemento da seção:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Como alternativa, instale a versão mais recente do Pacote de Hospedagem do ASP.NET Core. A versão mais recente é compatível com versões anteriores dos aplicativos do ASP.NET Core com suporte.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>processPath incorreto, variável de PATH ausente, pacote de hospedagem não instalado, sistema/IIS não reiniciado, Pacotes Redistribuíveis do VC++ não instalados ou violação de acesso de dotnet.exe

* **Navegador:** Erro HTTP 500,0-ANCM falha no carregamento do manipulador em processo

* **Log do aplicativo:** O aplicativo ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' com a raiz física ' C:\{PATH}\' falhou ao iniciar o processo com a linha de comando ' "{...}" ', ErrorCode = ' 0x80070002:0. Não foi possível iniciar o aplicativo '{PATH}'. O executável não foi encontrado em '{PATH}'. Falha ao iniciar o aplicativo '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

* **Log de depuração do módulo ASP.NET Core:** Log de eventos: ' o aplicativo ' {PATH} ' não pôde ser iniciado. O executável não foi encontrado em '{PATH}'. HRESULT com falha retornado: 0x8007023e

Solucionando problemas:

* Confirme se o aplicativo é executado localmente no Kestrel. Uma falha do processo pode ser o resultado de um problema no aplicativo. Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.

* Verifique o atributo *processPath* no elemento `<aspNetCore>` em *web.config* para confirmar se ele é `dotnet` para uma FDD (implantação dependente de estrutura) ou `.\{ASSEMBLY}.exe` para uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd).

* Para uma FDD, o *dotnet.exe* pode não estar acessível por meio das configurações de PATH. Confirme se *C:\Arquivos de Programas\dotnet\\* existe nas configurações de PATH do Sistema.

* Para uma FDD, o *dotnet.exe* pode não estar acessível para a identidade do usuário do pool de aplicativos. Confirme se a identidade do usuário do pool de aplicativos tem acesso ao diretório *C:\Arquivos de Programas\dotnet*. Confirme se não há nenhuma regra de negação configurada para a identidade do usuário do pool de aplicativos no *C:\Arquivos de Programas\dotnet* e nos diretórios do aplicativo.

* Talvez você tenha implantado uma FDD e instalado o .NET Core sem reiniciar o IIS. Reinicie o servidor ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.

* Você pode ter implantado uma FDD sem instalar o runtime do .NET Core no sistema de hospedagem. Se o runtime do .NET Core ainda não foi instalado, execute o **Instalador do Pacote de Hospedagem do .NET Core** no sistema.

  [Instalador de pacote de hospedagem do .NET Core atual (download direto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Se um runtime específico for necessário, baixe o runtime dos [Arquivos de Download do .NET](https://dotnet.microsoft.com/download/archives) e instale-o no sistema. Conclua a instalação reiniciando o sistema ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Argumentos incorretos do elemento \<aspNetCore>

* **Navegador:** Erro HTTP 500,0-ANCM falha no carregamento do manipulador em processo

* **Log do aplicativo:** Invocar hostfxr para localizar o manipulador de solicitação de inprocesso falhou sem encontrar nenhuma dependência nativa. Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador. Não foi possível localizar o manipulador de solicitação inprocess. Saída capturada da invocação de hostfxr: você pretendia executar comandos do SDK dotnet? Instale o SDK dotNet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 falhou ao iniciar o aplicativo '/LM/W3SVC/3/ROOT ', ErrorCode ' 0x8000ffff '.

* **Log de stdout do módulo ASP.NET Core:** Você quis dizer executar comandos do SDK dotnet? Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **Log de depuração do módulo ASP.NET Core:** Invocar hostfxr para localizar o manipulador de solicitação de inprocesso falhou sem encontrar nenhuma dependência nativa. Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador. Falha de HRESULT retornado: 0x8000ffff não pôde localizar o manipulador de solicitação de inprocesso. Saída capturada da invocação de hostfxr: você pretendia executar comandos do SDK dotnet? Instale o SDK do dotNet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 falha no HRESULT retornado: 0x8000ffff

Solucionando problemas:

* Confirme se o aplicativo é executado localmente no Kestrel. Uma falha do processo pode ser o resultado de um problema no aplicativo. Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.

* Examine o atributo *arguments* no elemento `<aspNetCore>` no *web.config* para confirmar se ele: (a) é `.\{ASSEMBLY}.dll` de uma FDD (implantação dependente de estrutura); ou (b) não está presente, é uma cadeia de caracteres vazia (`arguments=""`) ou uma lista de argumentos do aplicativo (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) para uma SCD (implantação autossuficiente).

## <a name="missing-net-core-shared-framework"></a>Estrutura compartilhada do .NET Core ausente

* **Navegador:** Erro HTTP 500,0-ANCM falha no carregamento do manipulador em processo

* **Log do aplicativo:** Invocar hostfxr para localizar o manipulador de solicitação de inprocesso falhou sem encontrar nenhuma dependência nativa. Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador. Não foi possível localizar o manipulador de solicitação inprocess. Saída capturada de invocação de hostfxr: não foi possível encontrar nenhuma versão de estrutura compatível. A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}', não foi encontrada.

Falha ao iniciar o aplicativo '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.

* **Log de stdout do módulo ASP.NET Core:** Não foi possível encontrar nenhuma versão de estrutura compatível. A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}', não foi encontrada.

* **Log de depuração do módulo ASP.NET Core:** HRESULT com falha retornado: 0x8000ffff

Solucionando problemas:

Para uma FDD (implantação dependente de estrutura), confirme se você tem o runtime correto instalado no sistema.

## <a name="stopped-application-pool"></a>Pool de aplicativos interrompido

* **Navegador:** 503 Serviço não disponível

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

* **Log de depuração do módulo ASP.NET Core:** O arquivo de log não foi criado.

Solucionando problemas:

Confirme que o Pool de Aplicativos não está no estado *Parado*.

## <a name="sub-application-includes-a-handlers-section"></a>O subaplicativo inclui uma seção \<manipuladores>

* **Navegador:** 500.19 Erro HTTP – erro interno do servidor

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log do aplicativo raiz é criado e mostra a operação normal. O arquivo de log do subaplicativo não é criado.

* **Log de depuração do módulo ASP.NET Core:** O arquivo de log do aplicativo raiz é criado e mostra a operação normal. O arquivo de log do subaplicativo não é criado.

Solucionando problemas:

Confirme se o arquivo *web.config* do subaplicativo não inclui uma seção `<handlers>` ou que o subaplicativo não herda os manipuladores do aplicativo pai.

A seção `<system.webServer>` do aplicativo pai de *web.config* é colocada dentro de um elemento `<location>`. A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo pai. Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.

## <a name="stdout-log-path-incorrect"></a>caminho do log de stdout incorreto

* **Navegador:** o aplicativo responde normalmente.

* **Log do aplicativo:** Não foi possível iniciar o redirecionamento stdout em C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Mensagem de exceção: HRESULT 0x80070005 retornado em {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84. Não foi possível parar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Mensagem de exceção: HRESULT 0x80070002 retornado em {PATH}. Não foi possível iniciar o redirecionamento de stdout em {PATH}\aspnetcorev2_inprocess.dll.

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

* **Log de depuração do módulo ASP.NET Core:** Não foi possível iniciar o redirecionamento stdout em C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Mensagem de exceção: HRESULT 0x80070005 retornado em {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84. Não foi possível parar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Mensagem de exceção: HRESULT 0x80070002 retornado em {PATH}. Não foi possível iniciar o redirecionamento de stdout em {PATH}\aspnetcorev2_inprocess.dll.

Solucionando problemas:

* O caminho `stdoutLogFile` especificado no elemento `<aspNetCore>` de *web.config* não existe. Para obter mais informações, consulte [módulo ASP.NET Core: criação e redirecionamento de log](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* O usuário do pool de aplicativos não tem acesso de gravação para o caminho do log de stdout.

## <a name="application-configuration-general-issue"></a>Problema geral de configuração do aplicativo

* **Navegador:** Erro HTTP 500,0-ANCM falha no carregamento do manipulador em processo **--ou--** erro http 500,30-ANCM falha no processo de inicialização

* **Log do aplicativo:** Ela

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log é criado, mas vazio ou criado com entradas normais até que o ponto do aplicativo falhe.

* **Log de depuração do módulo ASP.NET Core:** Ela

Solucionando problemas:

O processo não pôde ser iniciado, provavelmente, devido a um problema de programação ou configuração do aplicativo.

Para mais informações, consulte os seguintes tópicos:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Este tópico descreve os erros comuns e fornece conselhos de solução de problemas para erros específicos ao hospedar ASP.NET Core aplicativos no serviço de aplicativos do Azure e no IIS.

Para obter diretrizes gerais de solução de problemas, consulte <xref:test/troubleshoot-azure-iis>.

Colete as seguintes informações:

* Comportamento do navegador (código de status e mensagem de erro)
* Entradas do Log de Eventos do Aplicativo
  * Serviço de Aplicativo do Azure &ndash; Confira <xref:test/troubleshoot-azure-iis>.
  * IIS
    1. Selecione **Iniciar** no menu **Windows**, digite *Visualizador de Eventos* e pressione **Enter**.
    1. Depois que o **Visualizador de eventos** for aberto, expanda **Logs do Windows** > **aplicativo** na barra lateral.
* Entradas do log de depuração e stdout do Módulo do ASP.NET Core
  * Serviço de Aplicativo do Azure &ndash; Confira <xref:test/troubleshoot-azure-iis>.
  * IIS &ndash; Siga as instruções nas seções [Criação de log e redirecionamento](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) e [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do tópico Módulo do ASP.NET Core.

Compare as informações do erro para os erros comuns a seguir. Se uma correspondência for encontrada, siga o aviso de solução de problemas.

A lista de erros neste tópico não é exaustiva. Se você encontrar um erro não listado aqui, abra um novo problema usando o botão **Comentários sobre o Conteúdo** na parte inferior deste tópico com instruções detalhadas sobre como reproduzir o erro.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>O upgrade do sistema operacional removeu o Módulo do ASP.NET Core de 32 bits

**Log do Aplicativo:** a DLL do Módulo **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** falhou ao ser carregada. Os dados são o erro.

Solucionando problemas:

Arquivos que não são do sistema operacional no diretório **C:\Windows\SysWOW64\inetsrv** não são preservados durante um upgrade do sistema operacional. Se o Módulo do ASP.NET Core estiver instalado antes de uma atualização do sistema operacional e, em seguida, qualquer pool de aplicativos for executado no modo de 32 bits após uma atualização do sistema operacional, esse problema será encontrado. Após um upgrade do sistema operacional, repare o Módulo do ASP.NET Core. Veja [Instalar o pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Selecione **Reparar** ao executar o instalador.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Extensão de site ausente, extensões de site de 32 bits (x86) e 64 bits (x64) instaladas ou conjunto de bits incorreto do processo

*Aplica-se aos aplicativos hospedados pelos Serviços de Aplicativo do Azure.*

* **Navegador:** Erro HTTP 500,0-ANCM falha no carregamento do manipulador em processo

* **Log do aplicativo:** Invocar hostfxr para localizar o manipulador de solicitação de inprocesso falhou sem encontrar nenhuma dependência nativa. Não foi possível localizar o manipulador de solicitação inprocess. Saída capturada de invocação de hostfxr: não foi possível encontrar nenhuma versão de estrutura compatível. A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada. Falha ao iniciar o aplicativo '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.

* **Log de stdout do módulo ASP.NET Core:** Não foi possível encontrar nenhuma versão de estrutura compatível. A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada.

Solucionando problemas:

* Se estiver executando o aplicativo em um runtime de visualização, instale a extensão de site de 32 bits (x86) **ou** de 64 bits (x64) que corresponda ao número de bit do aplicativo e à versão de runtime do aplicativo. **Não instale extensões ou várias versões de runtime da extensão.**

  * Tempo de execução do ASP.NET Core {RUNTIME VERSION} (x86)
  * Tempo de execução do ASP.NET Core {RUNTIME VERSION} (x64)

  Reinicie o aplicativo. Aguarde vários segundos até que o aplicativo seja reiniciado.

* Se a execução do aplicativo em um runtime de visualização e as [extensões de site](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) de 32 bits (x86) e 64 bits (x64) estiverem instaladas, desinstale a extensão de site que não corresponde ao número de bit do aplicativo. Depois de remover a extensão de site, reinicie o aplicativo. Aguarde vários segundos até que o aplicativo seja reiniciado.

* Se executar o aplicativo em um runtime de visualização e o número de bit da extensão de site corresponder ao do aplicativo, confirme se a *versão do runtime* da extensão de site de visualização corresponde à versão do runtime do aplicativo.

* Confirme se a **Plataforma** do aplicativo em **Configurações do aplicativo** corresponde ao número de bit do aplicativo.

Para obter mais informações, consulte <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Um aplicativo x86 é implantado, mas o pool de aplicativos não está habilitado para aplicativos de 32 bits

* **Navegador:** Erro HTTP 500,30-ANCM falha no início do processo

* **Log do aplicativo:** O aplicativo '/LM/W3SVC/5/ROOT ' com a raiz física ' {PATH} ' atingiu uma exceção gerenciada inesperada, código de exceção = ' 0xe0434352 '. Verifique os logs de stderr para obter mais informações. Aplicativo '/LM/W3SVC/5/ROOT' com raiz física '{PATH}' falhou ao carregar o clr e o aplicativo gerenciado. O thread de trabalho do CLR foi encerrado prematuramente

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log é criado, mas vazio.

Esse cenário é interceptado pelo SDK ao publicar um aplicativo autocontido. O SDK produzirá um erro se o RID não coincidir com o destino da plataforma (por exemplo, RID `win10-x64` com `<PlatformTarget>x86</PlatformTarget>` no arquivo de projeto).

Solucionando problemas:

Para uma implantação dependente da estrutura x86 (`<PlatformTarget>x86</PlatformTarget>`), habilite o pool de aplicativos de IIS para aplicativos de 32 bits. No Gerenciador do IIS, abra as **Configurações Avançadas** do pool de aplicativos e defina **Habilitar Aplicativos de 32 Bits** como **Verdadeiro**.

## <a name="platform-conflicts-with-rid"></a>Conflitos de plataforma com o RID

* **Navegador:** 502.5 Erro HTTP – falha do processo

* **Log do aplicativo:** O aplicativo ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' com a raiz física ' C:\{PATH}\' falhou ao iniciar o processo com linha de comando ' "C:\{PATH} {ASSEMBLY}. {exe | dll} "', ErrorCode = ' 0x80004005: FF.

* **Log de stdout do módulo ASP.NET Core:** Exceção sem tratamento: System. BadImageFormatException: não foi possível carregar o arquivo ou assembly ' {ASSEMBLY}. dll '. Foi feita uma tentativa de carregar um programa com um formato incorreto.

Solucionando problemas:

* Confirme se o aplicativo é executado localmente no Kestrel. Uma falha do processo pode ser o resultado de um problema no aplicativo. Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.

* Se essa exceção ocorrer para uma implantação dos Aplicativos do Azure ao fazer upgrade de um aplicativo e implantar assemblies mais recentes, exclua manualmente todos os arquivos da implantação anterior. Assemblies incompatíveis remanescentes podem resultar em uma exceção `System.BadImageFormatException` durante a implantação de um aplicativo atualizado.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Ponto de extremidade de URI incorreto ou site interrompido

* **Navegador:** ERR_CONNECTION_REFUSED **--ou--** não é possível se conectar

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

Solucionando problemas:

* Confirme se o ponto de extremidade do URI correto para o aplicativo está sendo usado. Verifique as associações.

* Confirme que o site do IIS não está no estado *Parado*.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Recursos do servidor CoreWebEngine ou W3SVC desabilitados

**Exceção do Sistema Operacional:** os recursos CoreWebEngine e W3SVC do IIS 7.0 devem ser instalados para usar o Módulo do ASP.NET Core.

Solucionando problemas:

Confirme que a função e os recursos apropriados estão habilitados. Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Caminho físico do site incorreto ou aplicativo ausente

* **Navegador:** 403 Proibido – acesso negado **OU** 403.14 Proibido – o servidor Web está configurado para não listar o conteúdo deste diretório.

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

Solucionando problemas:

Confira as **Configurações Básicas** no site do IIS e a pasta do aplicativo físico. Confirme que o aplicativo está na pasta no **Caminho físico** do site do IIS.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Função incorreta, Módulo do ASP.NET Core Não Instalado ou permissões incorretas

* **Navegador:** 500.19 Erro interno do servidor – a página solicitada não pode ser acessada porque os dados de configuração relacionados da página são inválidos. **–OU–** Esta página não pode ser exibida

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

Solucionando problemas:

* Confirme que você habilitou a função apropriada. Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Abra **Programas e Recursos** ou **Aplicativos e Recursos** e confirme se a **Hospedagem do Windows Server** está instalada. Se a **Hospedagem do Windows Server** não estiver presente na lista de programas instalados, baixe e instale o Pacote de Hospedagem do .NET Core.

  [Instalador de pacote de hospedagem do .NET Core atual (download direto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Verifique se o **pool de aplicativos** > **modelo de processo** > **identidade** está definido como **ApplicationPoolIdentity** ou se a identidade personalizada tem as permissões corretas para acessar a pasta de implantação do aplicativo.

* Se você desinstalou o Pacote de Hospedagem do ASP.NET Core e instalou uma versão anterior do pacote de hospedagem, o arquivo *applicationHost.config* não inclui uma seção para o Módulo do ASP.NET Core. Abra *applicationHost.config* em *%windir%/System32/inetsrv/config* e encontre o grupo de seção `<configuration><configSections><sectionGroup name="system.webServer">`. Se estiver faltando a seção do Módulo do ASP.NET Core no grupo de seções, adicione o elemento da seção:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Como alternativa, instale a versão mais recente do Pacote de Hospedagem do ASP.NET Core. A versão mais recente é compatível com versões anteriores dos aplicativos do ASP.NET Core com suporte.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>processPath incorreto, variável de PATH ausente, pacote de hospedagem não instalado, sistema/IIS não reiniciado, Pacotes Redistribuíveis do VC++ não instalados ou violação de acesso de dotnet.exe

* **Navegador:** 502.5 Erro HTTP – falha do processo

* **Log do aplicativo:** O aplicativo ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' com a raiz física ' C:\{PATH}\' falhou ao iniciar o processo com a linha de comando ' "{...}" ', ErrorCode = ' 0x80070002:0.

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log é criado, mas vazio.

Solucionando problemas:

* Confirme se o aplicativo é executado localmente no Kestrel. Uma falha do processo pode ser o resultado de um problema no aplicativo. Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.

* Verifique o atributo *processPath* no elemento `<aspNetCore>` em *web.config* para confirmar se ele é `dotnet` para uma FDD (implantação dependente de estrutura) ou `.\{ASSEMBLY}.exe` para uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd).

* Para uma FDD, o *dotnet.exe* pode não estar acessível por meio das configurações de PATH. Confirme se *C:\Arquivos de Programas\dotnet\\* existe nas configurações de PATH do Sistema.

* Para uma FDD, o *dotnet.exe* pode não estar acessível para a identidade do usuário do pool de aplicativos. Confirme se a identidade do usuário do pool de aplicativos tem acesso ao diretório *C:\Arquivos de Programas\dotnet*. Confirme se não há nenhuma regra de negação configurada para a identidade do usuário do pool de aplicativos no *C:\Arquivos de Programas\dotnet* e nos diretórios do aplicativo.

* Talvez você tenha implantado uma FDD e instalado o .NET Core sem reiniciar o IIS. Reinicie o servidor ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.

* Você pode ter implantado uma FDD sem instalar o runtime do .NET Core no sistema de hospedagem. Se o runtime do .NET Core ainda não foi instalado, execute o **Instalador do Pacote de Hospedagem do .NET Core** no sistema.

  [Instalador de pacote de hospedagem do .NET Core atual (download direto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Se um runtime específico for necessário, baixe o runtime dos [Arquivos de Download do .NET](https://dotnet.microsoft.com/download/archives) e instale-o no sistema. Conclua a instalação reiniciando o sistema ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Argumentos incorretos do elemento \<aspNetCore>

* **Navegador:** 502.5 Erro HTTP – falha do processo

* **Log do aplicativo:** O aplicativo ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' com a raiz física ' C:\{PATH}\' falhou ao iniciar o processo com a linha de comando ' "dotnet". ASSEMBLY\{}. dll ', ErrorCode = ' 0x80004005:80008081.

* **Log de stdout do módulo ASP.NET Core:** O aplicativo a ser executado não existe: ' PATH\{ASSEMBLY}. dll '

Solucionando problemas:

* Confirme se o aplicativo é executado localmente no Kestrel. Uma falha do processo pode ser o resultado de um problema no aplicativo. Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.

* Examine o atributo *arguments* no elemento `<aspNetCore>` no *web.config* para confirmar se ele: (a) é `.\{ASSEMBLY}.dll` de uma FDD (implantação dependente de estrutura); ou (b) não está presente, é uma cadeia de caracteres vazia (`arguments=""`) ou uma lista de argumentos do aplicativo (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) para uma SCD (implantação autossuficiente).

Solucionando problemas:

Para uma FDD (implantação dependente de estrutura), confirme se você tem o runtime correto instalado no sistema.

## <a name="stopped-application-pool"></a>Pool de aplicativos interrompido

* **Navegador:** 503 Serviço não disponível

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

Solucionando problemas:

Confirme que o Pool de Aplicativos não está no estado *Parado*.

## <a name="sub-application-includes-a-handlers-section"></a>O subaplicativo inclui uma seção \<manipuladores>

* **Navegador:** 500.19 Erro HTTP – erro interno do servidor

* **Log do Aplicativo:** nenhuma entrada

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log do aplicativo raiz é criado e mostra a operação normal. O arquivo de log do subaplicativo não é criado.

Solucionando problemas:

Confirme se o arquivo *web.config* do subaplicativo não inclui uma seção `<handlers>`.

## <a name="stdout-log-path-incorrect"></a>caminho do log de stdout incorreto

* **Navegador:** o aplicativo responde normalmente.

* **Log do aplicativo:** Aviso: não foi possível criar \\stdoutLogFile?\{caminho} \ path_doesnt_exist \ stdout_ {ID do processo} _ {TIMESTAMP}. log, ErrorCode =-2147024893.

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log não foi criado.

Solucionando problemas:

* O caminho `stdoutLogFile` especificado no elemento `<aspNetCore>` de *web.config* não existe. Para obter mais informações, consulte [módulo ASP.NET Core: criação e redirecionamento de log](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* O usuário do pool de aplicativos não tem acesso de gravação para o caminho do log de stdout.

## <a name="application-configuration-general-issue"></a>Problema geral de configuração do aplicativo

* **Navegador:** 502.5 Erro HTTP – falha do processo

* **Log do aplicativo:** O aplicativo ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' com a raiz física ' C:\{PATH}\' processo criado com a linha de comando ' "C:\{PATH}\{ASSEMBLY}. {exe | dll} "", mas travou ou não respondeu ou não escutau na porta determinada ' {PORT} ', ErrorCode = ' {código de erro} '

* **Log de stdout do módulo ASP.NET Core:** O arquivo de log é criado, mas vazio.

Solucionando problemas:

O processo não pôde ser iniciado, provavelmente, devido a um problema de programação ou configuração do aplicativo.

Para mais informações, consulte os seguintes tópicos:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
