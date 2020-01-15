---
title: Hospedar o ASP.NET Core em um web farm
author: guardrex
description: Saiba como hospedar várias instâncias de um aplicativo ASP.NET Core com recursos compartilhados em um ambiente de web farm.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/web-farm
ms.openlocfilehash: 5c13e9bc4c514f9b42871d55a430265c8ec2da23
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75951826"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Hospedar o ASP.NET Core em um web farm

Por [Luke Latham](https://github.com/guardrex) e [Chris Ross](https://github.com/Tratcher)

Um *web farm* é um grupo de dois ou mais servidores web (ou *nós*) que hospedam várias instâncias de um aplicativo. Quando as solicitações de usuários chegam a um web farm, um *balanceador de carga* distribui as solicitações para os nós de farm da web. Os web farms melhoram:

* **Confiabilidade/disponibilidade** &ndash; quando um ou mais nós falham, o balanceador de carga pode rotear solicitações para outros nós de funcionamento para continuar processando solicitações.
* **Capacidade/desempenho** &ndash; vários nós podem processar mais solicitações do que um único servidor. O balanceador de carga equilibra a carga de trabalho ao distribuir as solicitações para os nós.
* **Escalabilidade** &ndash; quando mais ou menos capacidade é necessária, o número de nós ativos pode ser aumentado ou diminuído para corresponder à carga de trabalho. Tecnologias de plataforma de web farm, como o [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), podem adicionar ou remover nós por solicitação do administrador do sistema ou automaticamente sem a intervenção humana.
* A **manutenção** &ndash; nós de um Web farm pode contar com um conjunto de serviços compartilhados, o que resulta em um gerenciamento de sistema mais fácil. Por exemplo, os nós de um web farm podem contar com um servidor de banco de dados individual e um local de rede comum para recursos estáticos, como imagens e arquivos para download.

Este tópico descreve as configurações e dependências para aplicativos ASP.NET Core hospedados em um web farm que conta com recursos compartilhados.

## <a name="general-configuration"></a>Configuração geral

<xref:host-and-deploy/index>  
Aprenda como configurar ambientes de hospedagem e implantar aplicativos ASP.NET Core. Configure um gerenciador de processo em cada nó do web farm para automatizar o início e a reinicialização do aplicativo. Cada nó requer o runtime do ASP.NET Core. Para saber mais, confira os tópicos na área [Hospedar e implantar](xref:host-and-deploy/index) da documentação.

<xref:host-and-deploy/proxy-load-balancer>  
Saiba mais sobre a configuração para aplicativos hospedados por trás de servidores proxy e balanceadores de carga, o que muitas vezes oculta informações de solicitação importantes.

<xref:host-and-deploy/azure-apps/index>  
[Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) é um [serviço de plataforma de computação em nuvem da Microsoft](https://azure.microsoft.com/) para hospedar aplicativos Web, incluindo o ASP.NET Core. O Serviço de Aplicativo é uma plataforma totalmente gerenciada que fornece escalabilidade automática, balanceamento de carga, aplicação de patch e implantação contínua.

## <a name="app-data"></a>Dados do aplicativo

Quando um aplicativo é dimensionado para várias instâncias, pode haver um estado do aplicativo que exija o compartilhamento entre os nós. Se o estado for transitório, considere a possibilidade de compartilhar um [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Se o estado compartilhado exigir persistência, considere armazenar o estado compartilhado em um banco de dados.

## <a name="required-configuration"></a>Configuração necessária

O serviço de cache e a proteção de dados exigem uma configuração para aplicativos implantados em um web farm.

### <a name="data-protection"></a>Proteção de Dados

O [sistema de proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) é usado por aplicativos para proteger os dados. A proteção de dados baseia-se em um conjunto de chaves de criptografia armazenados em um *token de autenticação*. Quando o sistema de Proteção de dados é inicializado, ele aplica [as configurações padrão](xref:security/data-protection/configuration/default-settings) que armazenam localmente o token de autenticação. Sob a configuração padrão, um token de autenticação exclusivo é armazenado em cada nó do web farm. Consequentemente, cada nó do web farm não pode descriptografar os dados criptografados por um aplicativo em qualquer outro nó. A configuração padrão normalmente não é adequada para hospedagem de aplicativos em um web farm. Uma alternativa à implementação de um token de autenticação compartilhado é sempre rotear as solicitações do usuário para o mesmo nó. Para saber mais sobre a configuração do sistema de Proteção de dados para implantações de web farm, confira <xref:security/data-protection/configuration/overview>.

### <a name="caching"></a>Armazenamento em cache

Em um ambiente de web farm, o mecanismo de cache deve compartilhar os itens em cache pelos nós do web farm. O serviço de cache deve contar com um cache Redis comum, um banco de dados compartilhado do SQL Server ou uma implementação personalizada de cache que compartilha os itens em cache pelo web farm. Para obter mais informações, consulte <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Componentes dependentes

Os cenários a seguir não exigem configuração adicional, mas dependem de tecnologias que exigem configurações para web farms.

| Cenário | Depende de &hellip; |
| -------- | ------------------- |
| Autenticação | Proteção de dados (confira <xref:security/data-protection/configuration/overview>).<br><br>Para obter mais informações, consulte <xref:security/authentication/cookie> e <xref:security/cookie-sharing>. |
| Identity | Configuração e autenticação do banco de dados.<br><br>Para obter mais informações, consulte <xref:security/authentication/identity>. |
| Sessão | Proteção de dados (cookies criptografados) (confira <xref:security/data-protection/configuration/overview>) e cache (confira <xref:performance/caching/distributed>).<br><br>Para saber mais, confira [Estado de sessão e aplicativo: estado de sessão](xref:fundamentals/app-state#session-state). |
| TempData | Proteção de dados (cookies criptografados) (confira <xref:security/data-protection/configuration/overview>) ou sessão (confira [Estado de sessão e aplicativo: estado de sessão](xref:fundamentals/app-state#session-state)).<br><br>Para saber mais, consulte [Estado de sessão e aplicativo: TempData](xref:fundamentals/app-state#tempdata). |
| Antifalsificação | Proteção de dados (confira <xref:security/data-protection/configuration/overview>).<br><br>Para obter mais informações, consulte <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Solução de problemas

### <a name="data-protection-and-caching"></a>Proteção de dados e cache

Quando a proteção de dados ou cache não está configurada para um ambiente de web farm, erros intermitentes ocorrem quando as solicitações são processadas. Isso acontece porque os nós não compartilham os mesmos recursos e as solicitações do usuário não são sempre roteadas para o mesmo nó.

Considere um usuário que entra no aplicativo usando a autenticação de cookie. O usuário entra no aplicativo em um nó do web farm. Se a sua próxima solicitação chegar ao mesmo nó no qual ele se conectou, o aplicativo consegue descriptografar o cookie de autenticação e permite o acesso ao recurso do aplicativo. Se a sua próxima solicitação chegar em um nó diferente, o aplicativo não consegue descriptografar o cookie de autenticação a partir do nó em que o usuário entrou e a autorização do recurso solicitado falhará.

Quando qualquer um dos seguintes sintomas ocorrem **de forma intermitente**, o problema normalmente é rastreado, indicando a configuração incorreta de proteção de dados ou cache para um ambiente de web farm:

* Quebras de autenticação &ndash; o cookie de autenticação está configurado incorretamente ou não pode ser descriptografado. Falha de login OpenIdConnect ou OAuth (Facebook, Microsoft, Twitter) com o erro "Falha de correlação".
* Quebras de autorização &ndash; a identidade foi perdida.
* O estado de sessão perde os dados.
* Os itens em cache desaparecem.
* O TempData falhará.
* Os POSTs falham &ndash; a verificação antifalsificação falhará.

Para saber mais sobre a configuração de Proteção de dados para implantações de web farm, confira <xref:security/data-protection/configuration/overview>. Para saber mais sobre a configuração de cache para implantações de web farm, confira <xref:performance/caching/distributed>.

## <a name="obtain-data-from-apps"></a>Obter dados de aplicativos

Se os aplicativos do web farm forem capazes de responder às solicitações, obtenha as solicitações, conexão e dados adicionais dos aplicativos que usarem o middleware embutido de terminal. Para saber mais e obter um código de exemplo, consulte <xref:test/troubleshoot#obtain-data-from-an-app>.

## <a name="additional-resources"></a>Recursos adicionais

* A [extensão de script personalizado para o Windows](/azure/virtual-machines/extensions/custom-script-windows) &ndash; baixa e executa scripts em máquinas virtuais do Azure, o que é útil para configuração de pós-implantação e instalação de software.
* <xref:host-and-deploy/proxy-load-balancer>
