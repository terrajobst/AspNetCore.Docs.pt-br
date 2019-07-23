* `-F|--no-formatting`

  Um sinalizador cuja presença suprime a formatação da resposta HTTP.

* `-h|--header`

  Define um cabeçalho para a solicitação HTTP. Os dois formatos de valor a seguir são compatíveis:

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  Especifica um arquivo em que toda a resposta HTTP (incluindo cabeçalhos e corpo) deve ser gravada. Por exemplo, `--response "C:\response.txt"`. Se ainda não existir, o arquivo será criado.

* `--response:body`

  Especifica um arquivo em que o corpo da resposta HTTP deve ser gravado. Por exemplo, `--response:body "C:\response.json"`. Se ainda não existir, o arquivo será criado.

* `--response:headers`

  Especifica um arquivo em que os cabeçalhos da resposta HTTP devem ser gravados. Por exemplo, `--response:headers "C:\response.txt"`. Se ainda não existir, o arquivo será criado.

* `-s|--streaming`

  Um sinalizador cuja presença habilita o streaming da resposta HTTP.
