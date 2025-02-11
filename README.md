# Gerador_De_Senhas_Personalizadas
Projetinho pessoal feito em python (pretendo incrementar)

# Código
import secrets
import string
from cryptography.fernet import Fernet
import os

# Função para gerenciamento e salvamento das chaves
def gerar_chave():
    chave = Fernet.generate_key()
    with open("chave.key", "wb") as chave_file:
        chave_file.write(chave)


def carregar_chave():
    if not os.path.exists("chave.key"):
        print("Chave não encontrada. Gerando nova chave...")
        gerar_chave()
    with open("chave.key", "rb") as chave_file:
        return chave_file.read()


# Funções de criptografia
def salvar_em_arquivo_criptografado(senhas):
    chave = carregar_chave()
    fernet = Fernet(chave)
    senhas_texto = "\n".join(senhas).encode()
    senhas_criptografadas = fernet.encrypt(senhas_texto)
    with open("senhas_criptografadas.txt", "wb") as file:
        file.write(senhas_criptografadas)
    print("Senhas criptografadas salvas com sucesso no arquivo senhas_criptografadas.txt.")


def ler_e_descriptografar_arquivo():
    chave = carregar_chave()
    fernet = Fernet(chave)
    try:
        with open("senhas_criptografadas.txt", "rb") as file:
            senhas_criptografadas = file.read()
            senhas = fernet.decrypt(senhas_criptografadas).decode()
            print("Senhas descriptografadas:")
            print(senhas)
    except FileNotFoundError:
        print("Arquivo de senhas criptografadas não encontrado.")
    except Exception as e:
        print(f"Erro ao descriptografar as senhas: {e}")


# Função para gerar senhas
def gerar_senhas(number, length, chars):
    return [''.join(secrets.choice(chars) for _ in range(length)) for _ in range(number)]


def main():
    print("Bem-vindo ao Gerador de Senhas Personalizado!")

    while True:
        incluir_minusculas = input("Incluir letras minúsculas? (s/n): ").strip().lower() == 's'
        incluir_maiusculas = input("Incluir letras maiúsculas? (s/n): ").strip().lower() == 's'
        incluir_numeros = input("Incluir números? (s/n): ").strip().lower() == 's'
        incluir_simbolos = input("Incluir símbolos? (s/n): ").strip().lower() == 's'

        chars = ""
        if incluir_minusculas:
            chars += string.ascii_lowercase
        if incluir_maiusculas:
            chars += string.ascii_uppercase
        if incluir_numeros:
            chars += string.digits
        if incluir_simbolos:
            chars += "!@#$%¨&*().,<>_-+:;?/"
        if chars:
            break
        else:
            print("Você deve selecionar pelo menos uma categoria de caracteres!")

    while True:
        try:
            number = int(input("Quantidade de senhas requeridas: "))
            if number <= 0:
                raise ValueError("O número deve ser maior que zero.")
            break
        except ValueError as e:
            print(f"Entrada inválida: {e}")

    while True:
        try:
            length = int(input("Qual o tamanho desta senha? "))
            if length <= 0:
                raise ValueError("O tamanho deve ser maior que zero.")
            break
        except ValueError as e:
            print(f"Entrada inválida: {e}")

    senhas = gerar_senhas(number, length, chars)
    print("Suas novas senhas são:")
    for i, senha in enumerate(senhas, 1):
        print(f"{i}: {senha}")

    salvar_em_arquivo_criptografado(senhas)

    opcional = input("Deseja descriptografar e visualizar as senhas? (s/n): ").strip().lower()
    if opcional == 's':
        ler_e_descriptografar_arquivo()

    print("Obrigado por usar o Gerador de Senhas Personalizado!")


if __name__ == "__main__":
    main()
