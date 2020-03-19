O separador de `:` não funciona com chaves hierárquicas de variável de ambiente em todas as plataformas. `__`, o sublinhado duplo, é:

* Compatível com todas as plataformas. Por exemplo, o separador de `:` não é suportado pelo [bash](https://linuxhint.com/bash-environment-variables/), mas `__` é.
* Substituído automaticamente por um `:`