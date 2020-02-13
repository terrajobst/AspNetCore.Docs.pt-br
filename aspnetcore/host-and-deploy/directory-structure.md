---
title: Estrutura do diretório do ASP.NET Core
author: guardrex
description: Saiba mais sobre a estrutura do diretório de aplicativos publicados do ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: c3c05e6bc461ea4a3bfefa2c7a49d524562f7e5b
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172265"
---
# <a name="aspnet-core-directory-structure"></a>Estrutura do diretório do ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

O diretório *publish* contém os ativos implantáveis do aplicativo produzidos pelo comando [dotnet publish](/dotnet/core/tools/dotnet-publish). O diretório contém:

* Arquivos de aplicativo
* Arquivos de configuração
* Ativos estáticos
* Pacotes
* Um runtime ([somente implantação autocontida](/dotnet/core/deploying/#self-contained-deployments-scd))

| Tipo de Aplicativo | Estrutura de diretórios |
| -------- | ------------------- |
| [Executável dependente de estrutura (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>publish&dagger;<ul><li>Exibições&dagger; aplicativos MVC; Se as exibições não forem pré-compiladas</li><li>Páginas&dagger; aplicativos MVC ou Razor Pages, se as páginas não forem pré-compiladas</li><li>wwwroot&dagger;</li><li>*arquivos. dll</li><li>{nome do assembly}. deps. json</li><li>{nome do assembly}. dll</li><li>{nome do assembly} {. Extensão da extensão} *. exe* no Windows, nenhuma extensão no MacOS ou Linux</li><li>{nome do assembly}. pdb</li><li>{nome do assembly}. Views. dll</li><li>{nome do ASSEMBLY}. Views. pdb</li><li>{nome do ASSEMBLY}. runtimeconfig. JSON</li><li>Web. config (implantações do IIS)</li><li>createdump ([utilitário createdump do Linux](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>* . portanto (biblioteca de objetos compartilhados do Linux)</li><li>*. a (arquivo MacOS)</li><li>* . dylib (biblioteca dinâmica MacOS)</li></ul></li></ul> |
| [Implantação independente (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Exibições&dagger; aplicativos MVC, se as exibições não forem pré-compiladas</li><li>Páginas&dagger; aplicativos MVC ou Razor Pages, se as páginas não forem pré-compiladas</li><li>wwwroot&dagger;</li><li>arquivos *. dll</li><li>{NOME DO ASSEMBLY}.deps.json</li><li>{NOME DO ASSEMBLY}.dll</li><li>{NOME DO ASSEMBLY}.exe</li><li>{NOME DO ASSEMBLY}.pdb</li><li>{NOME DO ASSEMBLY}.Views.dll</li><li>{NOME DO ASSEMBLY}.Views.pdb</li><li>{NOME DO ASSEMBLY}.runtimeconfig.json</li><li>web.config (implantações do IIS)</li></ul></li></ul> |

&dagger;Indica um diretório

O diretório *publish* representa o *caminho raiz de conteúdo* (também chamado de *caminho base do aplicativo*) da implantação. Qualquer que seja o nome fornecido para o diretório *publish* do aplicativo implantado no servidor, o local dele serve como o caminho físico do servidor para o aplicativo hospedado.

O diretório *wwwroot*, se presente, contém somente ativos estáticos.

## <a name="additional-resources"></a>Recursos adicionais

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Implantação de aplicativos do .NET Core](/dotnet/core/deploying/)
* [Estruturas de destino](/dotnet/standard/frameworks)
* [Catálogo de RIDs do .NET Core](/dotnet/core/rid-catalog)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O diretório *publish* contém os ativos implantáveis do aplicativo produzidos pelo comando [dotnet publish](/dotnet/core/tools/dotnet-publish). O diretório contém:

* Arquivos de aplicativo
* Arquivos de configuração
* Ativos estáticos
* Pacotes
* Um runtime ([somente implantação autocontida](/dotnet/core/deploying/#self-contained-deployments-scd))

| Tipo de Aplicativo | Estrutura de diretórios |
| -------- | ------------------- |
| [Executável dependente de estrutura (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>publish&dagger;<ul><li>Exibições&dagger; aplicativos MVC; Se as exibições não forem pré-compiladas</li><li>Páginas&dagger; aplicativos MVC ou Razor Pages, se as páginas não forem pré-compiladas</li><li>wwwroot&dagger;</li><li>*arquivos. dll</li><li>{nome do assembly}. deps. json</li><li>{nome do assembly}. dll</li><li>{nome do assembly} {. Extensão da extensão} *. exe* no Windows, nenhuma extensão no MacOS ou Linux</li><li>{nome do assembly}. pdb</li><li>{nome do assembly}. Views. dll</li><li>{nome do ASSEMBLY}. Views. pdb</li><li>{nome do ASSEMBLY}. runtimeconfig. JSON</li><li>Web. config (implantações do IIS)</li><li>createdump ([utilitário createdump do Linux](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>* . portanto (biblioteca de objetos compartilhados do Linux)</li><li>*. a (arquivo MacOS)</li><li>* . dylib (biblioteca dinâmica MacOS)</li></ul></li></ul> |
| [Implantação independente (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Exibições&dagger; aplicativos MVC, se as exibições não forem pré-compiladas</li><li>Páginas&dagger; aplicativos MVC ou Razor Pages, se as páginas não forem pré-compiladas</li><li>wwwroot&dagger;</li><li>arquivos *. dll</li><li>{NOME DO ASSEMBLY}.deps.json</li><li>{NOME DO ASSEMBLY}.dll</li><li>{NOME DO ASSEMBLY}.exe</li><li>{NOME DO ASSEMBLY}.pdb</li><li>{NOME DO ASSEMBLY}.Views.dll</li><li>{NOME DO ASSEMBLY}.Views.pdb</li><li>{NOME DO ASSEMBLY}.runtimeconfig.json</li><li>web.config (implantações do IIS)</li></ul></li></ul> |

&dagger;Indica um diretório

O diretório *publish* representa o *caminho raiz de conteúdo* (também chamado de *caminho base do aplicativo*) da implantação. Qualquer que seja o nome fornecido para o diretório *publish* do aplicativo implantado no servidor, o local dele serve como o caminho físico do servidor para o aplicativo hospedado.

O diretório *wwwroot*, se presente, contém somente ativos estáticos.

Criar uma pasta *Logs* é útil para o [log de depuração aprimorado do Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). As pastas no caminho fornecido para o valor `<handlerSetting>` não são criadas automaticamente pelo módulo e devem existir previamente na implantação para permitir que o módulo grave o log de depuração.

Um diretório *Logs* pode ser criado para a implantação usando uma das duas abordagens a seguir:

* Adicione o seguinte elemento `<Target>` ao arquivo de projeto:

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   O elemento `<MakeDir>` cria uma pasta *Logs* vazia na saída publicada. O elemento usa a propriedade `PublishDir` para determinar o local de destino no qual criar a pasta. Vários métodos de implantação, como a Implantação da Web, ignoram pastas vazias durante a implantação. O elemento `<WriteLinesToFile>` gera um arquivo na pasta *Logs*, o que garante a implantação da pasta no servidor. A criação de pasta usando essa abordagem poderá falhar se o processo de trabalho não tiver acesso de gravação para a pasta de destino.

* Crie fisicamente o diretório *Logs* no servidor na implantação.

O diretório de implantação requer permissões de leitura/execução. O diretório *Logs* requer permissões de leitura/gravação. Diretórios adicionais em que os arquivos são gravados exigem permissões de leitura/gravação.

## <a name="additional-resources"></a>Recursos adicionais

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Implantação de aplicativos do .NET Core](/dotnet/core/deploying/)
* [Estruturas de destino](/dotnet/standard/frameworks)
* [Catálogo de RIDs do .NET Core](/dotnet/core/rid-catalog)

::: moniker-end
