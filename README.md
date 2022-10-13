# lancamentos
Classifica lançamentos com base em critério constante em cada arquivo .txt

Um XLS de lancamentos contem linhas de historico
uma pasta geral com arquivos TXT nomeados de cada favorecido contendo em cada linha expressoes que identifique o favorecido (nome do arquivo)

Script le cada arquivo e procura no historico as expressoes do arquivo (lista), caso encontre insere o nome do arquivo na coluna correspondente (loop)

PROBLEMA
No script montado, a cada arquivo lido, os dados são inseridos no local correto, POREM são apagados as informações corredas inseridas pela leitura do arquivo anterior, sendo que ao final, somente os dados do ultimo arquivo são salvos.

Esperado ;)

ANEX    107
DEBORA    87
ANEX    55.15%
DEBORA    44.85%


O atual retorno :(

ANEX    107
ANEX    100.00%

DEBORA    87
DEBORA    100.00%

Como realizar corretamente?
O arquivo gerado é usando no Power BI para criar a apresentação dos resultados.
