```console
npm run release
```

Este comando suspende o fornecimento dos ativos do lado do cliente ao executar o aplicativo. Os ativos são colocados na pasta *wwwroot*.

O Webpack concluiu as seguintes tarefas:

* Limpou os conteúdos do diretório *wwwroot*.
* Converteu o TypeScript para JavaScript, um processo conhecido como *transpilação*.
* Reduziu o tamanho do arquivo JavaScript gerado, um processo conhecido como *minificação*.
* Copiou os arquivos JavaScript, CSS e HTML processados do *src* para o diretório *wwwroot*.
* Injetou os seguintes elementos no arquivo *wwwroot/index.html*:
  * Uma marca `<link>`, que referencia o arquivo *wwwroot/main.\<hash\>.css*. Essa marca é colocada imediatamente antes do fim da marca `</head>`.
  * Uma marca `<script>`, que referencia o arquivo minificado *wwwroot/main.\<hash\>.js*. Essa marca é colocada imediatamente antes do fim da marca `</body>`.
