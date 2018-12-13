# <a name="aspnet-core-health-check-sample"></a>Amostra de verificação de integridade do ASP.NET Core

Esta amostra ilustra o uso dos serviços e do Middleware de Verificação de Integridade. Esta amostra apresenta os cenários descritos no tópico [Verificações de integridade no ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks).

Para executar o aplicativo de exemplo para um cenário descrito no tópico, use o comando [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) na pasta do projeto em um shell de comando. Passe uma opção para o cenário que você está explorando. O aplicativo usa como padrão a configuração `basic` quando uma opção não é fornecida para `dotnet run`.

| Cenário                                               | Comando de aplicativo de exemplo               | Descrição |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Investigação de integridade básica (padrão)                           | `dotnet run --scenario basic`    | Confirma que o aplicativo pode processar solicitações HTTP. |
| Investigação de banco de dados                                         | `dotnet run --scenario db`       | Verifica uma conexão do banco de dados do SQL Server. Confira a seção [Investigação de banco de dados](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) do tópico para obter instruções. |
| Investigações de preparação/atividade                              | `dotnet run --scenario liveness` | Executa verificações para confirmar um status de aplicativo em tempo real (*atividade*) em comparação com a preparação do aplicativo para se tornar ativo (*preparação*). |
| Investigação baseada em métrica (memória)/<br>gravador de resposta personalizada | `dotnet run --scenario writer`   | Verifica no uso de memória e grava o JSON personalizado quando o ponto de extremidade de integridade é verificado. |
| Filtrar por porta                                         | `dotnet run --scenario port`     | Filtra as verificações de integridade para uma porta específica. Confira a seção [Filtrar por porta](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) do tópico para obter instruções. |

Os cenários de investigação de banco de dados e filtro de porta exigem configuração adicional. Confira o tópico [Verificações de integridade](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) para obter detalhes.
