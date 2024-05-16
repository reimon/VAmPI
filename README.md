

# VAmPI
**A API Vulnerável** *(Baseada no OpenAPI 3)*
![vampi](https://i.imgur.com/zR0quKf.jpg)

[![Docker Image CI](https://github.com/erev0s/VAmPI/actions/workflows/docker-image.yml/badge.svg)](https://github.com/erev0s/VAmPI/actions/workflows/docker-image.yml) ![Docker Pulls](https://img.shields.io/docker/pulls/erev0s/vampi)


VAmPI é uma API vulnerável feita com Flask e inclui vulnerabilidades do top 10 da [OWASP](https://owasp.org/www-project-api-security/) para APIs. Foi criada porque eu queria uma API vulnerável para avaliar a eficiência de ferramentas usadas para detectar problemas de segurança em APIs. Inclui um interruptor para tornar a API vulnerável ou não durante os testes. Isso permite cobrir melhor os casos de falsos positivos/negativos. VAmPI também pode ser usada para fins de aprendizado/ensino. Você pode encontrar mais detalhes sobre as vulnerabilidades em [erev0s.com](https://erev0s.com/blog/vampi-vulnerable-api-security-testing/).


#### Características
 - Baseada no top 10 de vulnerabilidades da OWASP para APIs.
 - Especificações OpenAPI3 e coleção Postman incluídas.
 - Interruptor global para ter um ambiente vulnerável ou não.
 - Autenticação baseada em token (Ajuste o tempo de vida dentro de app.py)

O fluxo de ações do VAmPI é o seguinte: um usuário não registrado pode ver informações mínimas sobre os usuários fictícios incluídos na API. Um usuário pode se registrar e então fazer login para ser autorizado a usar o token recebido durante o login para postar um livro. Para um livro postado, os dados aceitos são o título e um segredo sobre esse livro. Cada livro é único para cada usuário e apenas o proprietário do livro deve ser permitido ver o segredo.

Um resumo rápido das ações incluídas pode ser visto na seguinte tabela:

| **Ação** |            **Caminho**           |                     **Detalhes**                    |
|:--------:|:--------------------------------:|:--------------------------------------------------:|
|     GET    |           /createdb           | Cria e popula o banco de dados com dados fictícios |
|     GET    |               /               |                     Página inicial do VAmPI                     |
|     GET    |           /users/v1           |      Exibe todos os usuários com informações básicas     |
|     GET    |        /users/v1/_debug       |         Exibe todos os detalhes de todos os usuários         |
|    POST    |       /users/v1/register      |                  Registrar novo usuário                 |
|    POST    |        /users/v1/login        |                   Login no VAmPI                   |
|     GET    |      /users/v1/{username}     |              Exibe usuário pelo nome de usuário             |
|   DELETE   |      /users/v1/{username}     |       Exclui usuário pelo nome de usuário (Apenas Admins)       |
|     PUT    |   /users/v1/{username}/email  |             Atualiza o email de um único usuário            |
|     PUT    | /users/v1/{username}/password |                Atualiza a senha do usuário               |
|     GET    |           /books/v1           |                 Recupera todos os livros                |
|    POST    |           /books/v1           |                    Adiciona novo livro                    |
|     GET    |        /books/v1/{book}       |      Recupera livro pelo título junto com o segredo     |

Para mais detalhes, você pode usar um serviço como o [editor swagger](https://editor.swagger.io) fornecendo a especificação OpenAPI que pode ser encontrada no diretório `openapi_specs`.


#### Lista de Vulnerabilidades
 - Injeção de SQL
 - Mudança de Senha Não Autorizada
 - Autorização de Objeto Quebrada
 - Atribuição em Massa
 - Exposição Excessiva de Dados através do endpoint de debug
 - Enumeração de Usuários e Senhas
 - RegexDOS (Negação de Serviço)
 - Falta de Recursos & Limitação de Taxa



 ## Executar
É uma aplicação Flask, então para executá-la você pode instalar todos os requisitos e depois rodar o `app.py`.
Para instalar todos os requisitos basta executar `pip3 install -r requirements.txt` e depois `python3 app.py`.

Ou, se preferir, você também pode executá-la através do docker ou docker compose.

 #### Executar através do Docker (Linux / Windows)

 - Disponível em [Dockerhub](https://hub.docker.com/repository/docker/reimon/apivampi/general)
~~~~
docker run -p 5000:5000 reimon/vampi:1
~~~~
#### Executar através do Docker (MACOS)
~~~~
docker run -p 5001:5000 reimon/vampi:1
~~~~
[Nota: se você executar o Docker em versões mais recentes do MacOS, use `-p 5001:5000` para evitar conflitos com o serviço AirPlay Receiver. Alternativamente, você poderia desativar o serviço AirPlay Receiver nas suas configurações de Sistema -> Compartilhamento.]

  #### Executar através do Docker Compose
`docker-compose` contém duas instâncias, uma instância com a configuração segura na porta 5001 e outra com insegura na porta 5002:
~~~~
docker-compose up -d
~~~~

## Personalizando o tempo de expiração do token e ambiente vulnerável ou não
Se você quiser alterar o tempo de expiração do token criado após o login ou se deseja mudar o ambiente para **não** ser vulnerável, então você pode usar algumas maneiras dependendo de como você executa a aplicação.

 - Se você executá-la normalmente com `python3 app.py`, então tudo o que você precisa fazer é editar as variáveis `alive` e `vuln` definidas no próprio `app.py`. A variável `alive` é medida em segundos, então se você colocar `100`, então o token expira após 100 segundos. A variável `vuln` é como booleana, se você definir como `1` então a aplicação é vulnerável, e se definir como `0` a aplicação não é vulnerável.
 - Se você executá-la através do Docker, então você deve passar variáveis de ambiente para o comando `docker run` ou editar o `Dockerfile` e reconstruir.
   - Exemplo de execução do Docker: `docker run -d -e vulnerable=0 -e tokentimetolive=300 -p 5000:5000 erev0s/vampi:latest`
     - Um recurso interessante de executá-la desta maneira é que você pode iniciar um 2º contêiner com `vulnerable=1` em uma porta diferente e alternar facilmente entre os dois.

   - No Dockerfile você encontrará duas variáveis de ambiente sendo definidas, o `ENV vulnerable=1` e o `ENV tokentimetolive=1200`. Sinta-se livre para alterá-las antes de executar o comando de construção do docker.


 [Imagem de freepik - www.freepik.com](https://www.freepik.com/vectors/party)

