# Tech Challenge Fase 2 — Gerenciamento de Restaurantes

Sistema de gerenciamento de restaurantes desenvolvido como parte do Tech Challenge Fase 2 da Pós-Graduação FIAP. O projeto implementa uma API REST com **Java 17**, **Spring Boot 3** e segue os princípios da **Clean Architecture**.

---

## Tecnologias

| Tecnologia | Versão |
|---|---|
| Java | 17 |
| Spring Boot | 3.2.3 |
| PostgreSQL | 15 |
| Lombok | 1.18.34 |
| MapStruct | 1.5.5.Final |
| SpringDoc OpenAPI (Swagger) | 2.3.0 |
| Docker / Docker Compose | — |

---

## Arquitetura

O projeto segue a **Clean Architecture**, com separação estrita entre as camadas:

```
src/main/java/br/com/techchallenge/
├── domain/                     # Núcleo do negócio (sem dependências externas)
│   ├── entity/                 # Entidades: Usuario, Restaurante, ItemCardapio
│   ├── valueobject/            # Value Objects: HorarioFuncionamento
│   └── gateway/                # Interfaces de repositório (contratos)
│
├── application/                # Casos de Uso e DTOs
│   ├── usecase/                # CadastrarUsuarioUseCase, CadastrarRestauranteUseCase, CadastrarItemCardapioUseCase
│   └── dto/                    # UsuarioDTO, RestauranteDTO, ItemCardapioDTO
│
└── infrastructure/             # Implementações técnicas (Spring, JPA, REST)
    ├── controller/             # Controllers REST
    ├── gateway/                # Implementações dos Gateways
    ├── persistence/            # Entidades JPA e Repositories
    └── config/                 # Configurações (OpenAPI)
```

---

## Endpoints da API

### Usuários — `/api/usuarios`

| Método | Endpoint | Descrição |
|---|---|---|
| `POST` | `/api/usuarios` | Cadastra um novo usuário |
| `GET` | `/api/usuarios` | Lista todos os usuários |
| `GET` | `/api/usuarios/{id}` | Busca usuário por ID |
| `DELETE` | `/api/usuarios/{id}` | Remove um usuário |

**Tipos de usuário:** `DONO_RESTAURANTE` \| `CLIENTE`

**Exemplo de body (POST):**
```json
{
  "nome": "João Silva",
  "email": "joao@email.com",
  "tipo": "DONO_RESTAURANTE"
}
```

---

### Restaurantes — `/api/restaurantes`

| Método | Endpoint | Descrição |
|---|---|---|
| `POST` | `/api/restaurantes` | Cadastra um novo restaurante |
| `GET` | `/api/restaurantes` | Lista todos os restaurantes |
| `GET` | `/api/restaurantes/{id}` | Busca restaurante por ID |
| `DELETE` | `/api/restaurantes/{id}` | Remove um restaurante |

> **Regra de negócio:** o `donoId` deve referenciar um usuário do tipo `DONO_RESTAURANTE`.

**Exemplo de body (POST):**
```json
{
  "nome": "Restaurante do João",
  "endereco": "Rua das Flores, 123",
  "tipoCozinha": "Italiana",
  "abertura": "11:00",
  "fechamento": "23:00",
  "donoId": "uuid-do-dono"
}
```

---

### Itens do Cardápio — `/api/itens-cardapio`

| Método | Endpoint | Descrição |
|---|---|---|
| `POST` | `/api/itens-cardapio` | Cadastra um item no cardápio |
| `GET` | `/api/itens-cardapio/restaurante/{restauranteId}` | Lista itens de um restaurante |
| `GET` | `/api/itens-cardapio/{id}` | Busca item por ID |
| `DELETE` | `/api/itens-cardapio/{id}` | Remove um item |

**Exemplo de body (POST):**
```json
{
  "nome": "Lasanha à Bolonhesa",
  "descricao": "Massa fresca com molho bolonhesa e bechamel",
  "preco": 45.90,
  "disponivelLocal": true,
  "caminhoFoto": "/fotos/lasanha.jpg",
  "restauranteId": "uuid-do-restaurante"
}
```

---

## Como Executar

### Com Docker Compose (recomendado)

Sobe a aplicação e o banco PostgreSQL em um único comando:

```bash
docker-compose up --build
```

A API estará disponível em `http://localhost:8080`.

### Localmente (sem Docker)

1. Certifique-se de ter um PostgreSQL rodando com os dados:
   - **Database:** `tech_challenge_db`
   - **User:** `user`
   - **Password:** `password`

2. Configure as variáveis de ambiente (ou `application.properties`):
   ```
   SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/tech_challenge_db
   SPRING_DATASOURCE_USERNAME=user
   SPRING_DATASOURCE_PASSWORD=password
   ```

3. Execute:
   ```bash
   mvn spring-boot:run
   ```

---

## Documentação Interativa (Swagger)

Com a aplicação em execução, acesse:

```
http://localhost:8080/swagger-ui.html
```

---

## Testes

```bash
mvn test
```

Os testes unitários cobrem os Casos de Uso com **JUnit 5** e **Mockito**, validando caminhos felizes e cenários de exceção (usuário não encontrado, tipo inválido, preço inválido).

---

## Collection do Postman

O arquivo `TechChallenge_Fase2_Collection.json` na raiz do projeto contém exemplos prontos de requisições para todos os endpoints. Importe-o diretamente no Postman.

