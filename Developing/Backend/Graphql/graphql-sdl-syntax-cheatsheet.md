# GraphQL SDL — основные синтаксические особенности (шпаргалка)

SDL (Schema Definition Language) описывает **схему** GraphQL: типы, поля, аргументы, директивы и т.д.

---

## 1) Базовые типы и поля

```graphql
type User {
  id: ID!
  email: String!
  name: String
  age: Int
  isActive: Boolean!
}
```

- `type` — объектный тип (то, что обычно возвращают `Query`/`Mutation`).
- `!` — **non-null**: значение не может быть `null`.
- Без `!` — поле допускает `null`.

---

## 2) Скалярные типы

Встроенные скаляры:
- `Int` (32-bit signed)
- `Float` (double)
- `String`
- `Boolean`
- `ID`

Кастомные скаляры:

```graphql
scalar DateTime
scalar Money
```

---

## 3) Списки и `!` (самая частая зона ошибок)

```graphql
friends: [User]      # список может быть null, элементы могут быть null
friends: [User]!     # список не null, элементы могут быть null
friends: [User!]     # список может быть null, элементы не null
friends: [User!]!    # список не null, элементы не null
```

Практический дефолт для коллекций: **`[T!]!`**.

---

## 4) Аргументы полей и значения по умолчанию

```graphql
type Query {
  user(id: ID!): User
  users(limit: Int = 20, offset: Int = 0): [User!]!
}
```

- Аргументы задаются как у функции: `field(arg: Type): ReturnType`.
- Default values применимы к аргументам и полям `input`.

---

## 5) Корневые типы: Query / Mutation / Subscription

```graphql
type Query {
  health: String!
  user(id: ID!): User
}

type Mutation {
  createUser(input: CreateUserInput!): User!
}

type Subscription {
  userCreated: User!
}
```

Переопределение корневых типов:

```graphql
schema {
  query: QueryRoot
  mutation: MutationRoot
  subscription: SubRoot
}
```

---

## 6) Input-типы (для аргументов)

```graphql
input CreateUserInput {
  email: String!
  name: String
  age: Int
}
```

Использование:

```graphql
type Mutation {
  createUser(input: CreateUserInput!): User!
}
```

---

## 7) Enum

```graphql
enum Role {
  ADMIN
  USER
  SUPPORT
}
```

---

## 8) Interface (контракт для типов)

```graphql
interface Node {
  id: ID!
}

type User implements Node {
  id: ID!
  email: String!
}

type Movie implements Node {
  id: ID!
  title: String!
}
```

---

## 9) Union (одно из нескольких типов)

```graphql
union SearchResult = User | Movie

type Query {
  search(q: String!): [SearchResult!]!
}
```

---

## 10) Директивы

### Встроенные директивы
- `@deprecated(reason: String)`
- `@include(if: Boolean!)`
- `@skip(if: Boolean!)`

Пример:

```graphql
type User {
  oldField: String @deprecated(reason: "Use `name` instead")
  name: String
}
```

### Пользовательские директивы

```graphql
directive @auth(role: Role!) on FIELD_DEFINITION | OBJECT

type Query {
  adminPanel: String @auth(role: ADMIN)
}
```

---

## 11) Описания и комментарии

Docstring (попадает в документацию / introspection):

```graphql
"""
Пользователь системы.
"""
type User {
  """Уникальный идентификатор"""
  id: ID!
}
```

Однострочный комментарий:

```graphql
# Это комментарий
```

---

## 12) Расширение типов: `extend`

Удобно для модульной схемы (разнесённой по файлам):

```graphql
type Query {
  health: String!
}

extend type Query {
  user(id: ID!): User
}
```

---

## 13) Практический шаблон «скелета схемы»

```graphql
schema {
  query: Query
  mutation: Mutation
}

scalar DateTime

enum Role { ADMIN USER }

interface Node { id: ID! }

type User implements Node {
  id: ID!
  email: String!
  role: Role!
  createdAt: DateTime!
}

input CreateUserInput {
  email: String!
  role: Role = USER
}

type Query {
  user(id: ID!): User
}

type Mutation {
  createUser(input: CreateUserInput!): User!
}
```

---

## 14) Быстрые правила (для ежедневной работы)

- Для списков чаще всего выбирай **`[T!]!`**.
- Для мутаций предпочитай один аргумент `input: SomeInput!`.
- Разделяй ответственность:
  - `type` — что возвращаем,
  - `input` — что принимаем.
- Для union/interface на клиенте почти всегда нужен `__typename` + фрагменты (в запросах).
