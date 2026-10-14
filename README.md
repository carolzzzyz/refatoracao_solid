# API de Livros — Refatoração SOLID

Projeto acadêmico em Java 21, Spring Boot 4.1.1, JPA e banco H2 em memória.

## Execução

Instale o JDK 21. Na pasta do projeto execute `./gradlew bootRun` (Linux/macOS) ou `gradlew.bat bootRun` (Windows). Na primeira execução, o Gradle precisa acessar a internet para baixar dependências.

Banco H2: `jdbc:h2:mem:refacdb`, usuário `sa`, senha vazia. O console H2, caso habilitado, é acessado por `/h2-console`.

## Endpoints

- `GET /livros`: listar.
- `POST /livros`: cadastrar (desconto aplicado conforme categoria).
- `PUT /livros/{id}`: atualizar (preserva preço informado, conforme implementação original).
- `DELETE /livros/{id}`: remover (bloqueia categoria `RARO`).

Exemplo de JSON:

```json
{"titulo":"Clean Code","autor":"Robert C. Martin","preco":120.0,"categoria":"TECNOLOGIA"}
```

Descontos **somente no cadastro**: ROMANCE 10%, TECNOLOGIA 20%, INFANTIL 30%, HQ 40%. Categorias não configuradas mantêm o preço. Cadastro retorna HTTP 201, exclusão 204, não encontrado 404 e entradas inválidas 400.

O envio de e-mail é **simulado via log**, como no projeto original; o relatório de quantidade também é registrado no log.

## SOLID aplicado

- **SRP**: controller recebe HTTP; `LivroServiceImpl` coordena regras; `CalculadoraDesconto` calcula; `RelatorioConsole` e `NotificadorConsole` cuidam das mensagens.
- **OCP**: novas categorias podem ser incluídas por implementações de `DescontoLivro` e registro de beans sem alterar o código da calculadora nem o controller.
- **LSP**: estratégias cumprem o contrato `DescontoLivro` e podem ser substituídas no cálculo sem alterar seus consumidores.
- **ISP**: substituímos `LivroOperations` (CRUD misturado com PDF, ERP, e-mail e etiquetas) por contratos específicos.
- **DIP**: controller depende de `LivroService`; serviço depende de `RelatorioLivros` e `NotificadorPromocional`, usando injeção por construtor. O acesso a dados permanece via abstração Spring Data `LivroRepository`.

## Testes

Execute `./gradlew test` ou `gradlew.bat test` para executar testes automatizados de CRUD, validação e restrição de exclusão.


