---
title: Metapacote Microsoft.AspNetCore.App para ASP.NET Core 2.1 ou posterior
author: Rick-Anderson
description: O metapacote Microsoft.AspNetCore.App inclui todos os pacotes do ASP.NET Core e Entity Framework Core compatíveis.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/21/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 913e3d83fbf1af7ea995a88202f86c60b359a7e2
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085659"
---
# <a name="microsoftaspnetcoreapp-metapackage-for-aspnet-core-21-or-later"></a>Metapacote Microsoft.AspNetCore.App para ASP.NET Core 2.1 ou posterior

Este recurso exige o ASP.NET Core 2.1 ou posterior direcionado ao .NET Core 2.1 ou posterior.

O [metapacote](/dotnet/core/packages#metapackages) [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) para ASP.NET Core:

* Não tem dependências de terceiros, com exceção de [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) e [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/). Essas dependências de terceiros são consideradas necessárias para garantir o funcionamento dos principais recursos das estruturas.
* Inclui todos os pacotes com suporte pela equipe do ASP.NET Core, exceto aqueles que contêm dependências de terceiros (que não sejam aqueles mencionados anteriormente).
* Inclui todos os pacotes com suporte pela equipe do Entity Framework Core, exceto aqueles que contêm dependências de terceiros (que não sejam aqueles mencionados anteriormente).

Todos os recursos do ASP.NET Core 2.1 e posterior e do Entity Framework Core 2.1 e posterior são incluídos no pacote `Microsoft.AspNetCore.App`. Os modelos de projeto padrão direcionados para ASP.NET Core 2.1 e posterior usam este pacote. Recomendamos que aplicativos voltados para o ASP.NET Core 2.1 e posterior e o Entity Framework Core 2.1 e posterior usem o pacote `Microsoft.AspNetCore.App`.

O número de versão do metapacote `Microsoft.AspNetCore.App` representa a versão mínima do ASP.NET Core e a versão do Entity Framework Core.

O uso do metapacote `Microsoft.AspNetCore.App` fornece restrições de versões que protegem seu aplicativo:

* Se um pacote incluído tem uma dependência (não direta) transitiva em um pacote no `Microsoft.AspNetCore.App`, e os números de versão forem diferentes, o NuGet gera um erro.
* Outros pacotes adicionados ao seu aplicativo não podem alterar a versão dos pacotes incluídos no `Microsoft.AspNetCore.App`.
* A consistência de versão garante uma experiência confiável. `Microsoft.AspNetCore.App` foi projetado para evitar combinações de versão não testado de bits relacionados que estão sendo usados juntos no mesmo aplicativo.

Aplicativos que usam o metapacote `Microsoft.AspNetCore.App` aproveitam automaticamente a estrutura compartilhada do ASP.NET Core. Quando você usa o metapacote `Microsoft.AspNetCore.App`, **nenhum** ativo dos pacotes NuGet do ASP.NET Core referenciados é implantado com o aplicativo, porque a estrutura compartilhada do ASP.NET Core contém esses ativos. Os ativos na estrutura compartilhada são pré-compilados para melhorar o tempo de inicialização do aplicativo. Saiba mais em [A estrutura compartilhada](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

O seguinte arquivo de projeto referencia o metapacote `Microsoft.AspNetCore.App` do ASP.NET Core e representa um modelo típico do ASP.NET Core 2.1:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

A marcação anterior representa um modelo típico de ASP.NET Core 2.1 e posterior. Ela não especifica um número de versão para a referência de pacote `Microsoft.AspNetCore.App`. Quando a versão não for especificada, uma versão [implícita](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) será especificada pelo SDK, ou seja, `Microsoft.NET.Sdk.Web`. Recomendamos que você conte com a versão implícita especificada pelo SDK, e não defina explicitamente o número de versão na referência de pacote. Se tiver dúvidas sobre essa abordagem, deixe um comentário do GitHub na [Discussão para a versão implícita do Microsoft.AspNetCore.App](https://github.com/aspnet/AspNetCore.Docs/issues/6430).

A versão implícita é definida como `major.minor.0` para aplicativos portátil. O mecanismo de roll forward estrutura compartilhada executará o aplicativo na versão compatível mais recente entre as estruturas compartilhadas instaladas. Para garantir que a mesma versão seja usada no desenvolvimento, no teste e na produção, certifique-se de que a mesma versão da estrutura compartilhada seja instalada em todos os ambientes. Para aplicativos independentes, o número de versão implícita é definido como `major.minor.patch` da estrutura compartilhada incluída no SDK instalado.

Especificar um número de versão na referência `Microsoft.AspNetCore.App` **não** garante que a versão da estrutura compartilhada será escolhida. Por exemplo, suponha que a versão "2.1.1" foi especificada, mas "2.1.3" está instalada. Nesse caso, o aplicativo usará "2.1.3". Embora não seja recomendado, você pode desabilitar o roll forward (patch e/ou secundária). Para obter mais informações sobre como efetuar roll forward do host dotnet e como configurar seu comportamento, veja [Efetuar roll forward do host dotnet](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker range="= aspnetcore-2.1"

`<Project Sdk` deve ser definido como `Microsoft.NET.Sdk.Web` para usar o `Microsoft.AspNetCore.App` da versão implícita. Quando `<Project Sdk="Microsoft.NET.Sdk">` (sem o `.Web` à direita) é usado:

* O aviso a seguir é gerado:

  *Aviso NU1604: a dependência de projeto Microsoft.AspNetCore.App não tem um limite inferior inclusivo. Inclua um limite inferior na versão de dependência para garantir resultados consistentes de restauração.*

* Esse é um problema conhecido do SDK do .NET Core 2.1.

::: moniker-end

<a name="update"></a>

## <a name="update-aspnet-core"></a>Atualizar o ASP.NET Core

O `Microsoft.AspNetCore.App` [metapacote](/dotnet/core/packages#metapackages) não é um pacote tradicional atualizado do NuGet. Semelhante ao `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` representa um tempo de execução compartilhado, que tem semântica de controle de versão especial tratada fora do NuGet. Para obter mais informações, veja [Pacotes, metapacotes e estruturas](/dotnet/core/packages).

Para atualizar o ASP.NET Core:

* Em computadores de desenvolvimento e servidores de compilação: faça download e instale o [SDK do .NET Core](https://www.microsoft.com/net/download).
* Em servidores de implantação: faça download e instale o [tempo de execução do .NET Core](https://www.microsoft.com/net/download).

 Os aplicativos efetuarão roll forward para a versão mais recente instalada na reinicialização do aplicativo. Não é necessário atualizar o número de versão `Microsoft.AspNetCore.App` no arquivo de projeto. Para obter mais informações, consulte [Roll forward de aplicativos dependentes de estrutura](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Se seu aplicativo tiver usado `Microsoft.AspNetCore.All`, veja [Migração do Microsoft.AspNetCore.All para Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).
