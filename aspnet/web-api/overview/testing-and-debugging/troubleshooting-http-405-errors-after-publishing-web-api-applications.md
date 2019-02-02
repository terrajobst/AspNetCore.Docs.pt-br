---
uid: web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
title: Solução de problemas de HTTP 405 erros após a publicação de aplicativos da Web API | Microsoft Docs
author: rmcmurray
description: Este tutorial descreve como solucionar problemas de erros HTTP 405 após publicar um aplicativo de API da Web para um servidor web de produção.
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 07ec7d37-023f-43ea-b471-60b08ce338f7
msc.legacyurl: /web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
msc.type: authoredcontent
ms.openlocfilehash: ce5b617cc1032d190cc2450aa554b462ea6f6156
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667408"
---
# <a name="troubleshooting-http-405-errors-after-publishing-web-api-applications"></a>Solução de problemas de erros HTTP 405 após a publicação de aplicativos de API da Web

> Este tutorial descreve como solucionar problemas de erros HTTP 405 após publicar um aplicativo de API da Web para um servidor web de produção.
> 
> ## <a name="software-used-in-this-tutorial"></a>Software usado neste tutorial
> 
> 
> - [Serviços de informações da Internet (IIS)](https://www.iis.net/) (versão 7 ou posterior)
> - [API Web](../../index.md) 


Aplicativos de API da Web normalmente usam vários verbos HTTP comuns: GET, POST, PUT, DELETE e, às vezes, aplicar PATCHES. Dito isso, os desenvolvedores podem deparar com situações em que esses verbos são implementados por outro módulo do IIS em seu servidor de produção, o que leva a uma situação em que um controlador de API da Web que funciona corretamente no Visual Studio ou em um servidor de desenvolvimento retornará um HTTP 405 erro quando ele é implantado em um servidor de produção. Felizmente, esse problema é resolvido facilmente, mas a resolução garante uma explicação de por que o problema está ocorrendo.

## <a name="what-causes-http-405-errors"></a>O que causa erros HTTP 405

A primeira etapa para aprender como erros HTTP 405 de problemas é entender o que um erro HTTP 405 realmente significa. O controle principal de documento para HTTP é [RFC 2616](http://www.ietf.org/rfc/rfc2616.txt), que define o código de status HTTP 405 como ***método não permitido***e descreve ainda mais esse código de status como uma situação em que &quot;o método especificada na linha de solicitação não é permitida para o recurso identificado pelo URI da solicitação.&quot; Em outras palavras, o verbo HTTP não é permitido para a URL específica que um cliente HTTP solicitado.

Como uma breve revisão, aqui estão vários métodos HTTP mais usados conforme definido na RFC 2616, RFC 4918 e RFC 5789:

| Método HTTP | Descrição |
| --- | --- |
| **GET** | Esse método é usado para recuperar dados de um URI e ele provavelmente o método HTTP mais usados. |
| **HEAD** | Esse método é muito parecido com o método GET, exceto que ele realmente não recupera os dados do URI da solicitação – ele simplesmente recupera o status HTTP. |
| **POST** | Esse método normalmente é usado para enviar novos dados para o URI; POST geralmente é usado para enviar dados de formulário. |
| **PUT** | Esse método normalmente é usado para enviar dados brutos para o URI; PUT é geralmente usado para enviar dados JSON ou XML para aplicativos de API da Web. |
| **DELETE** | Esse método é usado para remover dados de um URI. |
| **OPTIONS** | Normalmente, esse método é usado para recuperar a lista de métodos HTTP que têm suporte para um URI. |
| **MOVIMENTAÇÃO DE CÓPIA** | Esses dois métodos são usados com o WebDAV e sua finalidade é auto-explicativo. |
| **MKCOL** | Esse método é usado com o WebDAV e ele é usado para criar uma coleção (por exemplo, um diretório) no URI especificado. |
| **PROPFIND PROPPATCH** | Esses dois métodos são usados com o WebDAV e eles são usados para consultar ou definir propriedades para um URI. |
| **DESBLOQUEIO DE BLOQUEIO** | Esses dois métodos são usados com o WebDAV e elas são usadas para bloquear/desbloquear o recurso identificado pelo URI de solicitação durante a criação. |
| **PATCH** | Esse método é usado para modificar um recurso existente do HTTP. |

Quando um dos seguintes métodos HTTP é configurado para uso no servidor, o servidor responderá com o status HTTP e outros dados que é apropriados para a solicitação. (Por exemplo, um método GET pode receber um HTTP 200 ***Okey*** resposta e um método PUT podem receber um HTTP 201 ***criado*** resposta.)

Se o método HTTP não está configurado para uso no servidor, o servidor responderá com um HTTP 501 ***não implementado*** erro.

No entanto, quando um método HTTP está configurado para uso no servidor, mas ele foi desabilitado para um determinado URI, o servidor responderá com um HTTP 405 ***método não permitido*** erro.

## <a name="example-http-405-error"></a>Erro HTTP 405 de exemplo

O seguinte exemplo de solicitação HTTP e resposta ilustram uma situação em que um cliente HTTP está tentando colocar o valor a um aplicativo de API da Web em um servidor web e o servidor retornará um erro HTTP que estados que o método PUT não é permitidos:


Solicitação de HTTP:


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample1.cmd)]


Resposta de HTTP:


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample2.cmd)]


Neste exemplo, o cliente HTTP enviou uma solicitação JSON válida para a URL para um aplicativo de API da Web em um servidor web, mas o servidor retornou uma mensagem de erro HTTP 405 que indica que o método PUT não foi permitido para a URL. Por outro lado, se o URI da solicitação não correspondeu a uma rota para o aplicativo de API da Web, o servidor retornaria um HTTP 404 ***não foi encontrado*** erro.

## <a name="resolve-http-405-errors"></a>Resolver erros HTTP 405

Há vários motivos por que um verbo HTTP específico talvez não seja permitido, mas há um cenário principal que é a causa desse erro no IIS: vários manipuladores são definidos para o mesmo verbo/método e um dos manipuladores está bloqueando o manipulador esperado processamento da solicitação. Por meio de explicação, IIS processa manipuladores do primeiro a pela última vez com base nas entradas do manipulador ordem arquivos applicationHost. config e Web. config, em que a primeira combinação de correspondência de caminho, verbo, recursos, etc., será usada para manipular a solicitação.

O exemplo a seguir é um trecho de um arquivo applicationHost. config para um servidor IIS que estava retornando um erro HTTP 405 ao usar o método PUT para enviar dados para um aplicativo de API da Web. Neste trecho, vários manipuladores HTTP são definidos e cada manipulador tem um conjunto diferente de métodos HTTP para o qual ele está configurado – a última entrada na lista é o manipulador de conteúdo estático, que é o manipulador padrão que é usado depois que os outros manipuladores tenham tido um chanc e para examinar a solicitação:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample3.xml)]

No exemplo acima, o manipulador de WebDAV e o manipulador de URL sem extensão para o ASP.NET (que é usado para a API da Web) são claramente definidas para listas separadas dos métodos HTTP. Observe que o manipulador de DLL ISAPI esteja configurado para todos os métodos HTTP, embora essa configuração não será necessariamente causar um erro. No entanto, as definições de configuração como essa precisam ser considerados ao solucionar problemas de erros HTTP 405.

No exemplo acima, o manipulador de DLL ISAPI não era o problema; Na verdade, o problema não foi definido no arquivo applicationHost. config para o servidor IIS - o problema foi causado por uma entrada que foi feita no arquivo Web. config quando o aplicativo de API da Web foi criado no Visual Studio. O seguinte trecho do arquivo da Web. config do aplicativo mostra o local do problema:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample4.xml)]

Neste trecho, o manipulador de URL sem extensão para o ASP.NET é redefinido para incluir métodos HTTP adicionais que serão usados com o aplicativo de API da Web. No entanto, como um conjunto semelhante de métodos HTTP é definido para o manipulador de WebDAV, ocorre um conflito. Nesse caso específico, o manipulador de WebDAV é definido e carregado pelo IIS, mesmo que o WebDAV está desabilitado para o site que inclui o aplicativo de API da Web. Durante o processamento de uma solicitação HTTP PUT, o IIS chama o módulo de WebDAV, pois ele está definido para o verbo PUT. Quando o módulo de WebDAV é chamado, ele verifica sua configuração e vê que ela está desabilitada, portanto, ele retornará um HTTP 405 ***método não permitido*** erro para qualquer solicitação que se parece com uma solicitação de WebDAV. Para resolver esse problema, você deve remover o WebDAV na lista de módulos HTTP para o site em que seu aplicativo de API da Web é definido. O exemplo a seguir mostra o que a que talvez seja a aparência:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample5.xml)]

Esse cenário costuma ser encontrado depois que um aplicativo é publicado de um ambiente de desenvolvimento para um ambiente de produção, e isso ocorre porque a lista de módulos/manipuladores é diferente entre ambientes de desenvolvimento e produção. Por exemplo, se você estiver usando o Visual Studio 2012 ou posterior para desenvolver um aplicativo de API da Web, o IIS Express é o servidor de web padrão para teste. Esse servidor web de desenvolvimento é uma versão reduzida do que a funcionalidade completa do IIS que é fornecido com um produto de servidor, e esse servidor web de desenvolvimento contém algumas alterações que foram adicionadas para cenários de desenvolvimento. Por exemplo, o módulo de WebDAV geralmente é instalado em um servidor web de produção que está executando a versão completa do IIS, embora ele não pode estar em uso real. A versão de desenvolvimento do IIS (IIS Express), instala o módulo de WebDAV, mas as entradas para o módulo de WebDAV são intencionalmente comentadas, portanto, o módulo de WebDAV nunca é carregado no IIS Express, a menos que você altere especificamente a sua configuração de IIS Express configurações para adicionar funcionalidade WebDAV para sua instalação IIS Express. Como resultado, seu aplicativo web pode funcionar corretamente em seu computador de desenvolvimento, mas você pode encontrar erros HTTP 405 quando você publica seu aplicativo de API da Web para seu servidor web de produção.

## <a name="summary"></a>Resumo

HTTP 405 erros são causados quando um método HTTP não é permitido por um servidor web para uma URL solicitada. Essa condição geralmente é vista quando um determinado manipulador tiver sido definido para um verbo específico e esse manipulador está substituindo o manipulador que você espera para processar a solicitação.

Se você encontrar uma situação em que você receber uma mensagem de erro HTTP 501, o que significa que a funcionalidade específica não foi implementada no servidor, isso geralmente significa que não há nenhum manipulador definido nas configurações do IIS que corresponda à solicitação de HTTP, que provavelmente indica que algo não foi instalado corretamente em seu sistema, ou algo tiver modificado as configurações do IIS para que haja que nenhum manipulador defini esse método de suporte o HTTP específico. Para resolver esse problema, você precisa reinstalar a qualquer aplicativo que está tentando usar um método HTTP para os quais ele tem nenhum módulo correspondente ou definições de manipulador.
