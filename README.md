import mysql.connector
from random import randint
from tabulate import tabulate
import re
def cria_database():
    nome_tabela=input('Digite Nome DataBase: ')
    ler=mysql.connector.connect(
        host='localhost',
        user='root',
        password=''
    )
    lerler=ler.cursor()
    lerler.execute(f'CREATE DATABASE {nome_tabela}')
    print(lerler.rowcount,'Criado!')
    menu()

def criacao_tabela():
    nome_tabela2 = input('Nome DataBase: ')
    nome_tabela=input('Nome tabela: ')
    ler = mysql.connector.connect(
        host='localhost',
        user='root',
        password='',
        database=nome_tabela2
    )
    lerler = ler.cursor()
    lerler.execute(
        (f'''CREATE TABLE {nome_tabela} (
                                    id INT AUTO_INCREMENT PRIMARY KEY,
                                    MATRICULA INT,
                                    EMAIL VARCHAR(255),
                                    NOME VARCHAR(255),
                                    SOBRENOME VARCHAR(255),
                                    CPF VARCHAR(11),
                                    FILIACAO VARCHAR(255),
                                    IDADE INT,
                                    RENDA FLOAT,
                                    ESCOLARIDADE VARCHAR(255)
                                )'''))
    menu()

def inserir_tabela():
    nome_tabela = input('DataBase: ')
    nome_tabela2 = input('Tabela: ')
    ler = mysql.connector.connect(
        host='localhost',
        user='root',
        password='',
        database=nome_tabela
    )
    lerler = ler.cursor()
    while True:
        n = randint(1000, 5000)
        n1 = input('Email: ')
        if not re.match(r'^[\w\.-]+@[\w\.-]+\.\w+$', n1):
            print("Email inválido.")
            continue

        n2 = input("Digite Nome: ")
        if not n2.isalpha():
            print("Nome deve conter apenas letras.")
            continue

        n3 = input('Digite Sobrenome: ')
        if not n3.isalpha():
            print("Sobrenome deve conter apenas letras.")
            continue

        while True:
            n4 = input('Digite CPF: ')
            if len(n4) == 11 and n4.isdigit():
                break
            else:
                print('CPF deve ter 11 números')

        n5 = input('Filiação: ')
        n6 = input('Idade: ')
        if not n6.isdigit() or int(n6) <= 0:
            print("Idade inválida.")
            continue

        n7 = input('Renda: ')
        try:
            n7 = float(n7)
            if n7 < 0:
                print("Renda inválida.")
                continue
        except ValueError:
            print("Renda inválida.")
            continue

        n8 = input('Escolaridade: ')

        try:
            sql = f"INSERT INTO {nome_tabela2}(MATRICULA, EMAIL, NOME, SOBRENOME, CPF, FILIACAO, IDADE, RENDA, ESCOLARIDADE)  VALUES('{n}','{n1}','{n2}','{n3}','{n4}','{n5}','{n6}','{n7}','{n8}')"
            lerler.execute(sql)
            ler.commit()
            print(lerler.rowcount, 'Inserido!')
        except mysql.connector.Error as erro:
            print("Erro ao inserir dados:", erro)

        resp = input('Quer Continuar?: ').strip().lower()
        if resp == 'n':
            break

    menu()


def dele():
    nome_tabela2=input('DataBase: ')
    nome_tabela = input('Qual tabela deletar?: ')
    try:
        ler = mysql.connector.connect(
            host='localhost',
            user='root',
            password='',
            database=nome_tabela2
        )
        lerler = ler.cursor()
        lerler.execute(f"DROP TABLE {nome_tabela}")
        print(f'A tabela {nome_tabela} foi excluída com sucesso!')
        ler.commit()
        menu()
    except mysql.connector.Error as erro:
        print("Erro ao excluir tabela:", erro)
        menu()

def exibir_tabela():
    nome_tab=input("Digite Database: ")
    ler = mysql.connector.connect(
        host='localhost',
        user='root',
        password='',
        database=nome_tab
    )
    lerler = ler.cursor()
    nome_tabela=input('Qual tabela?: ')
    sql = f"SELECT * FROM {nome_tabela}"
    lerler.execute(sql)
    colunas = [coluna[0] for coluna in lerler.description]
    resultados = lerler.fetchall()
    lerler.close()
    ler.close()
    if not resultados:
        print("Nenhum dado encontrado na tabela.")
    else:
        tabela = tabulate(resultados, headers=colunas, tablefmt='pretty')
        print(tabela)

    menu()
def buscar_usuario():
    nome_tab = input("Digite Database: ")
    nome_tabela = input('Qual tabela?: ')
    campo_busca = input("Digite 'CPF' para buscar pelo CPF ou 'Nome' para buscar pelo nome: ")
    if campo_busca.lower() == 'cpf':
        valor = input("Digite o CPF do usuário: ")
        coluna = 'CPF'
    elif campo_busca.lower() == 'nome':
        valor = input("Digite o nome do usuário: ")
        coluna = 'NOME'
    else:
        print("Opção inválida.")
        menu()
        return

    try:
        ler = mysql.connector.connect(
            host='localhost',
            user='root',
            password='',
            database=nome_tab
        )
        lerler = ler.cursor()
        sql = f"SELECT * FROM {nome_tabela} WHERE {coluna} = '{valor}'"
        lerler.execute(sql)
        resultados = lerler.fetchall()
        lerler.close()
        ler.close()
        if not resultados:
            print(f"Nenhum usuário encontrado com {coluna} = '{valor}'.")
        else:
            colunas = [coluna[0] for coluna in lerler.description]
            tabela = tabulate(resultados, headers=colunas, tablefmt='pretty')
            print(tabela)
    except mysql.connector.Error as erro:
        print("Erro ao buscar usuário:", erro)

    menu()


def menu():
    print()
    print('1.Criar DataBase\n2.Criar Tabela\n3.Deletar Tabela\n4.Exibir Tabela\n5.Inserir Dados\n6.Buscar usuario\n7.Sair')
    resp=input('Escolha Uma Opção: ')
    match resp:
        case '1':
            cria_database()
        case '2':
            criacao_tabela()
        case '3':
            dele()
        case '4':
            exibir_tabela()
        case '5':
            inserir_tabela()
        case '6':
            buscar_usuario()

        case '7':
            exit()
        case _:
            print('Não existe!!')
            menu()

menu()
