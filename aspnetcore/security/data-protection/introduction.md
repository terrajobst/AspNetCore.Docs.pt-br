---
title: Proteção de dados do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o conceito de proteção de dados e os princípios de design de APIs de proteção de dados de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/introduction
ms.openlocfilehash: 37f170a3e8a46ef2215b0999358d46dd402636df
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897983"
---
# <a name="aspnet-core-data-protection"></a>Proteção de dados do ASP.NET Core

Aplicativos da Web geralmente precisam armazenar dados confidenciais de segurança. Windows fornece DPAPI para aplicativos da área de trabalho, mas isso é inadequado para aplicativos da web. A pilha de proteção de dados do ASP.NET Core fornecem uma API de criptografia simple e fácil de usar um desenvolvedor pode usar para proteger dados, incluindo rotação e gerenciamento de chaves.

A pilha de proteção de dados do ASP.NET Core foi projetada para servir como a substituição de longo prazo para o &lt;machineKey&gt; elemento no ASP.NET 1. x - 4. x. Ele foi projetado para resolver muitos dos problemas da pilha de criptografia antigo, fornecendo uma solução para a maioria dos casos de uso, os aplicativos modernos têm probabilidade de encontrar.

## <a name="problem-statement"></a>Declaração do problema

A declaração do problema geral pode ser declarada sucintamente em uma única sentença: Eu preciso manter informações confiáveis para recuperação posterior, mas eu não confio o mecanismo de persistência. Em termos de web, isso pode ser escrito como "Eu preciso de ida e volta estado confiável por meio de um cliente não confiável."

O exemplo canônico disso é um cookie de autenticação ou portador token. O servidor gera um "Estou Groot e ter permissões de xyz" de token e entrega-o para o cliente. Em uma data futura, o cliente apresentará esse token de volta para o servidor, mas o servidor precisa de algum tipo de garantia de que o cliente não foi forjada o token. Portanto, o primeiro requisito: autenticidade (também conhecido como integridade e à prova de adulteração).

Uma vez que o estado persistente é confiável pelo servidor, estimamos que esse estado pode conter informações específicas para o ambiente operacional. Isso pode ser na forma de um caminho de arquivo, uma permissão, um identificador ou outra referência indireta, ou alguma outra parte dos dados específicos do servidor. Essas informações, em geral, não deveriam ser divulgadas para um cliente não confiável. Portanto, o segundo requisito: confidencialidade.

Por fim, como aplicativos modernos são divididos em componentes, o que vimos é componentes individuais vai querer tirar proveito desse sistema sem levar em consideração outros componentes no sistema. Por exemplo, se um componente de token de portador é usando essa pilha, ele deve operar sem interferência de um mecanismo de anti-CSRF que também pode usar a mesma pilha. Portanto, o requisito final: isolamento.

Podemos fornecer ainda mais as restrições para limitar o escopo de nossos requisitos. Vamos supor que todos os serviços que operam com o sistema de criptografia são igualmente confiáveis e que os dados não precisam ser gerado ou consumido fora os serviços sob nosso controle direto. Além disso, exigimos que as operações são tão rápidas quanto possível, pois cada solicitação ao serviço web pode percorrer o sistema criptográfico uma ou mais vezes. Isso torna a criptografia simétrica ideal para o nosso cenário, e podemos pode desconto criptografia assimétrica até que tal uma vez que ele é necessário.

## <a name="design-philosophy"></a>Filosofia de design

Começamos por meio da identificação de problemas com a pilha existente. Depois que tivermos, nós pesquisamos o panorama das soluções existentes e concluiu que não há solução existente teve muito os recursos que são procurados. Em seguida, projetamos uma solução baseada em vários princípios de orientação.

* O sistema deve oferecer a simplicidade da configuração. O ideal é que o sistema seria sem nenhuma configuração e os desenvolvedores poderiam parta para a ação. Em situações em que os desenvolvedores precisam configurar um aspecto específico (como o repositório de chaves), consideração deve ser fornecida a fazer essas configurações específicas simples.

* Oferecem uma API voltado ao consumidor simples. As APIs devem ser fáceis de usar corretamente e difícil de usar incorretamente.

* Os desenvolvedores não devem aprender os princípios de gerenciamento de chaves. O sistema deve lidar com a seleção de algoritmo e a vida útil da chave em nome do desenvolvedor. O ideal é que o desenvolvedor nunca mesmo deve ter acesso ao material da chave bruto.

* As chaves devem ser protegidas em repouso quando possível. O sistema deve descobrir um mecanismo de proteção padrão apropriado e aplicá-la automaticamente.

Com esses princípios em mente, desenvolvemos um simples [fácil de usar](xref:security/data-protection/using-data-protection) pilha da proteção de dados.

As APIs de proteção de dados do ASP.NET Core não destinam principalmente para persistência indefinida de cargas confidenciais. Outras tecnologias, como [DPAPI do Windows CNG](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) e [do Azure Rights Management](/rights-management/) são mais adequados para o cenário de armazenamento indefinido, e eles têm recursos de gerenciamento de chave forte de forma correspondente. Dito isso, não há nada proibindo um desenvolvedor que usa as APIs de proteção de dados do ASP.NET Core para proteção de longo prazo de dados confidenciais.

## <a name="audience"></a>Público-alvo

O sistema de proteção de dados é dividido em cinco pacotes principais. Vários aspectos dessas APIs três principal públicos-alvo; de destino

1. O [visão geral das APIs de consumidor](xref:security/data-protection/consumer-apis/overview) os desenvolvedores de aplicativo e estrutura de destino.

   "Quero saber mais sobre como funciona a pilha de ou sobre como ela é configurada. Simplesmente quero executar alguma operação no tão simples uma maneira possível com a alta probabilidade de usar as APIs com êxito".

2. O [APIs de configuração](xref:security/data-protection/configuration/overview) os desenvolvedores de aplicativos e administradores de sistema de destino.

   "Eu preciso informar ao sistema de proteção de dados de que meu ambiente requer configurações ou os caminhos não padrão."

3. Os desenvolvedores de destino APIs de extensibilidade responsável pela implementação de política personalizada. O uso dessas APIs seria limitado a situações raras e experientes, os desenvolvedores de reconhecimento de segurança.

   "Eu preciso substituir um componente inteiro dentro do sistema porque tenho requisitos comportamentais realmente exclusivos. Desejo saber usados raramente partes da superfície de API a fim de criar um plug-in que atenda aos meus requisitos."

## <a name="package-layout"></a>Layout do pacote

A pilha da proteção de dados consiste em cinco pacotes.

* [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) contém o <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> e <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> interfaces para criar serviços de proteção de dados. Ele também contém métodos de extensão úteis para trabalhar com esses tipos (por exemplo, [IDataProtector.Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). Se o sistema de proteção de dados é instanciado em outro lugar e você está consumindo a API, referência `Microsoft.AspNetCore.DataProtection.Abstractions`.

* [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) contém a implementação principal do sistema de proteção de dados, incluindo operações criptográficas de núcleo, gerenciamento de chaves, configuração e extensibilidade. Para instanciar o sistema de proteção de dados (por exemplo, adicioná-lo para um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>) ou fazer referência a modificar ou estender seu comportamento, `Microsoft.AspNetCore.DataProtection`.

* [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contém APIs adicionais que os desenvolvedores podem ser úteis, mas que não cabem no pacote principal. Por exemplo, este pacote contém métodos de fábrica para instanciar o sistema de proteção de dados para armazenar chaves em um local no sistema de arquivos sem injeção de dependência (consulte <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>). Ele também contém métodos de extensão para limitar o tempo de vida de cargas protegidas (consulte <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector>).

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) pode ser instalado em um aplicativo existente do ASP.NET 4.x para redirecionar seu `<machineKey>` operações para usar a nova pilha de proteção de dados do ASP.NET Core. Para obter mais informações, consulte <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Microsoft.AspNetCore.Cryptography.KeyDerivation](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) fornece uma implementação da rotina de hash de senha de PBKDF2 e podem ser usadas por sistemas que precisam lidar com as senhas de usuário com segurança. Para obter mais informações, consulte <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Recursos adicionais

<xref:host-and-deploy/web-farm>
