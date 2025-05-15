# Exemplo de TDD

Este projeto demonstra a aplicação dos princípios de Test-Driven Development (TDD)

## Funcionalidades Implementadas com TDD

* **Cadastrar Usuário:**
    * Verificação de cadastro com dados válidos (nome, e-mail, senha).
    * Impedimento de cadastro com e-mail já existente.
    * Validação de campos obrigatórios.
      
## Tecnologias Utilizadas

* Linguagem de Programação: [**Python**]
* Framework de Testes: [**PyTest**]

## Pré-requisitos

Antes de executar o projeto, certifique-se de ter instalado:

* [**Python 3.x**]
* [**PyTest**] (Você pode instalar com: `pip install pytest`)


## Observações

* Este projeto foi desenvolvido seguindo os princípios de TDD, com os testes sendo escritos antes da implementação do código da funcionalidade.
* Mocks foram utilizados para simular o acesso ao banco de dados, focando na lógica de negócio.

## Passo 1 - Começando Pelos Testes

criaremos em uma pasta "tests" um arquivo Python: test_cadastro_usuario.py. Nele definiremos todos os testes para as possíveis respostas do sistema ao tentar cadastrar um usuário:

class Test_Cadastro:

````python
repositorio = UsuarioRepositorio()
servico = UsuarioServico(repositorio)

try:
    # Cadastro de usuário
    novo_usuario = servico.cadastrar_usuario("João da Silva", "joao@email.com", "senha123")
    print(f"Usuário cadastrado: {novo_usuario.nome}, {novo_usuario.email}")

    # Tentativa de cadastrar com email existente
    servico.cadastrar_usuario("Maria Souza", "joao@email.com", "outra_senha")

except ValueError as e:
    print(f"Erro ao cadastrar: {e}")

try:
    # Login bem-sucedido
    usuario_logado = servico.logar_usuario("joao@email.com", "senha123")
    print(f"Usuário logado: {usuario_logado.nome}")

    # Login com senha incorreta
    servico.logar_usuario("joao@email.com", "senha_errada")

except ValueError as e:
    print(f"Erro ao logar: {e}")

try:
    # Cadastro com campos vazios
    servico.cadastrar_usuario("", "email@email.com", "senha")

except ValueError as e:
    print(f"Erro ao cadastrar: {e}")

````


Estes testes não rodarão pois estão com erros, afinal não criamos as classes "Usuario", "UsuarioRepositorio" e "UsuarioServiço" ainda, mas é importante entender que todas as possíveis operações/funcionalidades dessa classe estão garantidamente testadas.

## Passo 2 - Criando a Classe

````python

class Usuario:
    def __init__(self, nome, email, senha):
        self.nome = nome
        self.email = email
        self.senha = senha

class UsuarioRepositorio:
    def __init__(self):
        self.usuarios = {}  # Simula um banco de dados

    def salvar(self, usuario):
        self.usuarios[usuario.email] = usuario

    def buscar_por_email(self, email):
        return self.usuarios.get(email)

class UsuarioServico:
    def __init__(self, repositorio):
        self.repositorio = repositorio

    def cadastrar_usuario(self, nome, email, senha):
        if not nome or not email or not senha:
            raise ValueError("Todos os campos são obrigatórios.")

        if self.repositorio.buscar_por_email(email):
            raise ValueError("E-mail já cadastrado.")

        novo_usuario = Usuario(nome, email, senha)
        self.repositorio.salvar(novo_usuario)
        return novo_usuario

    def logar_usuario(self, email, senha):
        usuario = self.repositorio.buscar_por_email(email)
        if not usuario:
            raise ValueError("E-mail inválido.")

        if usuario.senha != senha:
            raise ValueError("Senha inválida.")

        return usuario
````


## Passo 3 - Executando os testes

Antes de executarmos, uma boa prática é criamos um ambiente virtual para executarmos nosso código Python, utilizaremos o módulo "venv" disponivel no próprio Python, ele replicará nosso ambiente em um ambiente a parte onde podemos instalar e testar dependencias sem usar nossa própria máquina.

````
python -m venv venv
````

Ativar o ambiente:

````
venv\Scripts\activate
````

Perceba que em seu cmd já estamos em um ambiente virtual (venv) e podemos instalar nossas dependências, no caso o pytest que executará nosso testes em Python:

````
pip install pytest
````

Execute-o e garanta que todos os testes passaram:

````
pytest
````
