---
title: Módulo do ASP.NET Core
author: guardrex
description: Saiba como o Módulo do ASP.NET Core permite que o servidor Web Kestrel use o IIS ou o IIS Express.
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/30/2018
uid: fundamentals/servers/aspnet-core-module
ms.openlocfilehash: d3f3a42dd7aebc425905b865376a584bcf0e5153
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861453"
---
# <a name="aspnet-core-module"></a>Módulo do ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl) e [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-2.2"

O módulo do ASP.NET Core permite que os aplicativos ASP.NET Core sejam executados em um processo de trabalho do IIS (*em processo*) por trás do IIS em uma configuração de proxy reverso (*fora do processo*). O IIS fornece recursos avançados de segurança e capacidade de gerenciamento de aplicativo Web.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O Módulo do ASP.NET Core permite que os aplicativos ASP.NET Core sejam executados por trás do IIS em uma configuração de proxy reverso. O IIS fornece recursos avançados de segurança e capacidade de gerenciamento de aplicativo Web.

::: moniker-end

Versões do Windows compatíveis:

* Windows 7 ou posterior
* Windows Server 2008 R2 ou posterior

::: moniker range=">= aspnetcore-2.2"

Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, o Servidor HTTP do IIS (`IISHttpServer`).

Ao hospedar de fora do processo, o módulo só funciona com o Kestrel. O módulo é incompatível com o [HTTP.sys](xref:fundamentals/servers/httpsys) (chamado anteriormente de [WebListener](xref:fundamentals/servers/weblistener)).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O módulo só funciona com o Kestrel. O módulo é incompatível com o [HTTP.sys](xref:fundamentals/servers/httpsys) (chamado anteriormente de [WebListener](xref:fundamentals/servers/weblistener)).

::: moniker-end

## <a name="aspnet-core-module-description"></a>Descrição de Módulo do ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:

* Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).

* Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).

### <a name="in-process-hosting-model"></a>Modelo de hospedagem em processo

Usando uma hospedagem em processo, um aplicativo ASP.NET Core é executado no mesmo processo que seu processo de trabalho do IIS. Isso remove a penalidade de desempenho de solicitações de criação de proxies pelo adaptador de loopback ao usar o modelo de hospedagem de fora do processo. O IIS manipula o gerenciamento de processos com o [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

O Módulo do ASP.NET Core:

* Executa a inicialização do aplicativo.
  * Carrega o [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Chama `Program.Main`.
* Manipula o tempo de vida da solicitação nativa do IIS.

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado em processo:

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-inprocess.png)

A solicitação chega da Web para o driver do HTTP.sys no modo kernel. O driver roteia as solicitações nativas ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS). O módulo recebe a solicitação nativa e a passa para o Servidor HTTP do IIS (`IISHttpServer`). O Servidor HTTP do IIS é uma implementação de servidor em processo do IIS que converte a solicitação de nativa para gerenciada.

Depois que o Servidor HTTP do IIS processa a solicitação, a solicitação é enviada por push para o pipeline de middleware do ASP.NET Core. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo. A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente que iniciou a solicitação.

### <a name="out-of-process-hosting-model"></a>Modelo de hospedagem de fora do processo

Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos. O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha. Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

As solicitações chegam da Web para o driver do HTTP.sys no modo kernel. O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS). O módulo encaminha as solicitações ao Kestrel em uma porta aleatória para o aplicativo, que não seja a porta 80/443.

O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o middleware de integração do IIS configura o servidor para escutar em `http://localhost:{port}`. Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas. O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.

Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo. O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel. A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.

Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos. O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha. Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

As solicitações chegam da Web para o driver do HTTP.sys no modo kernel. O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS). O módulo encaminha as solicitações ao Kestrel em uma porta aleatória para o aplicativo, que não seja a porta 80/443.

O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o middleware de integração do IIS configura o servidor para escutar em `http://localhost:{port}`. Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas. O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.

Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo. O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel. A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.

::: moniker-end

Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos. Para saber mais sobre módulos do IIS ativos com o módulo, confira <xref:host-and-deploy/iis/modules>.

O Módulo do ASP.NET Core tem algumas outras funções. O módulo pode:

* Definir variáveis de ambiente para o processo de trabalho.
* Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.
* Encaminhar tokens de autenticação do Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Como instalar e usar o Módulo do ASP.NET Core

Para obter instruções detalhadas sobre como instalar e usar o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/index>. Para obter informações sobre como configurar o módulo, confira o <xref:host-and-deploy/aspnet-core-module>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* [Repositório do GitHub do Módulo do ASP.NET Core (código-fonte)](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
