<h1>Situação </h1>

Tenho um relatório de lançamento contendo as seguintes colunas:

Documento
Origem
Cliente	
Código Transação
Nome
Data
Valor
Histórico

<h1>Necessidade</h1>

Atribuir a um comprador cada operação com base no Histórico	

Criei arquivos de texto (.TXT) onde cada linha corresponde a uma palavra/expressão vinculada a um vendedor/comprador
Ex: Silvio.txt
PRF-7I93
Tesla
Paris
Dubai

Maycon.txt
Madrid
Manchester
Mineirão
BOB-0Y01

<h2>Vamos a solução empregada:</h2>


# Passo 1: Importar bibliotecas necessárias para a automação
import pandas as pd
import os

# Passo 2: Ler o arquivo excel (.XLS) dom o pandas
tabela = pd.read_excel(r"/Users/silviobsjunior/Documents/Anex/Registro do movimento financeiro BAN.XLS")

# Passo 3 - Remover colunas(column) nao necessarias
tabela = tabela.drop(tabela.columns[[11,12,13,14,15]], axis=1)

# Passo 4 - Remover colunas vazias
tabela = tabela.dropna(how="all", axis=1)

# Passo 5 - Remover linhas com pelo menos um valor vazio, assim pego as linhas de cabeçalho do relatório
tabela = tabela.dropna(how="any", axis=0)
tabela, tabela.columns = tabela[1:] , tabela.iloc[0]

# Passo 6 - Alterar cabeçalho de colunas(rows) para tirar acentuação
tabela.columns = tabela.columns.str.replace('Código transação', 'Transacao')
tabela.columns = tabela.columns.str.replace('Histórico', 'Historico')

# Passo 7 - Set the index of the DataFrame to the Codigo
tabela = tabela.set_index("Codigo")
tabela.head()
tabela

# Passo 8 - Remover as linhas(rows) com transações indesejadas:
tabela.drop(tabela.index[tabela["Transacao"] == 30], inplace=True)
tabela.drop(tabela.index[tabela["Transacao"] == 31], inplace=True)

# Passo 9 - Inserir nova coluna(column) na posição especifica para classificar com base na transação - entrada/saida
tabela.insert(6,'Tipo', "" )

# Passo 10  - Adiciona tipo com base em valor
tabela.loc[tabela["Transacao"] == 29, "Tipo"] = "Debito"
tabela.loc[tabela["Transacao"] == 34, "Tipo"] = "Credito"
tabela.loc[tabela["Transacao"] == 35, "Tipo"] = "Debito"

# Passo 11 - Analise Inicial para ver quantos lançamentos foram classificados e se a quantidade bate com o total de lançamentos
display(tabela["Transacao"].value_counts())
display(tabela["Transacao"].value_counts(normalize=True).map("{:.2%}".format))

# Passo 12 -  Inserir nova coluna(column) em uma posição especifica
tabela.insert(6,'Favorecido', "" )


# Passo 13 - Ler os arquivos na pasta para pesquisar se consta no histórico as palavras/expressões

accepted_extensions = ["txt", "TXT"]

pasta = '/Users/silviobsjunior/Documents/Anex/favorecidos'

for diretorio, subpastas, arquivos in os.walk(pasta):
   arquivos = sorted([fn for fn in arquivos if fn.split(".")[-1] in accepted_extensions])
    for arquivo in arquivos:
       arqs = os.path.join(diretorio, arquivo)

        grades = open(arqs).readlines()
        contents = []

        for i in range(len(grades)):
            contents.append(grades[i].strip('\n'))

#Função para checar ser uma das palavras/expressões da lista(list) contem no histórico e retornar com o nome do arquivo sem a extensão para ser inserido na coluna favorecido ou nada para a linha ficar elegível para a próxima pesquisa

        def check_value(x):
            for i in contents:
                if i.lower() in x.lower():
                    return os.path.splitext(arquivo)[0].upper()
            return None

        
        for index, row in tabela.iterrows():
           if row['Favorecido'] == None or row['Favorecido'] == "":
               row['Favorecido'] = check_value(str(row['Historico'])) 
        


# Passo 14 - Analise Inicial
display(tabela["Favorecido"].value_counts())
display(tabela["Favorecido"].value_counts(normalize=True).map("{:.2%}".format))

# Passo 15 - Exportar o arquivo atualizado para ser usado como base de dados no Power BI e apresentar meus indicadores
tabela.to_csv("/Users/silviobsjunior/Documents/Anex/RMFinBAN.csv", index=False, mode='w')


// FIM

Eu estava usando uma outra forma para localizar quais linhas que iriam receber o nome do arquivo, porém a cada arquivo os demais linhas tinha a coluna favorecido que retornou False apagado seu conteúdo, ficando somente identificado o favorecido do ultimo arquivo(.txt) lido

#tabela['Favorecido'] = tabela['Historico'].apply(lambda x: check_value(x))
Não sei como inserir(se possível) uma condicional na lambda para pesquisar somente as linhas onde a coluna favorecido ainda não conste valor inserido.
