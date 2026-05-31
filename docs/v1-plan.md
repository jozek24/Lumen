# Lumen — v1 Plan

> Cel v1: MCP Server działa z Claude Desktop. Józef może zapytać Claude'a o swój kontekst.
> Definicja "done": wpisujesz w Claude Desktop "co wiem o sobie?" i dostajesz odpowiedź z Lumena.

---

## Zakres v1

✅ W zakresie:
- GitHub repo + struktura projektu
- Docker Compose (PostgreSQL lokalnie)
- Backend — Clean Architecture, warstwy, encje
- Auth — Clerk
- CRUD kontekstu (Facts / Temporary / Insights)
- MCP Server (get_context, add_note)
- GitHub Actions (podstawowy pipeline)

❌ Poza zakresem v1:
- Frontend React (v2)
- AI chat nad kontekstem (v2)
- Embeddingi + pgvector + semantic search (v3)
- Deploy (v3)

---

## Encje w Domenie

```csharp
User
  Id (Guid)
  ClerkId (string)
  CreatedAt (DateTime)

ContextEntry
  Id (Guid)
  UserId (Guid)
  Type (enum: Fact | Temporary | Insight)
  Content (string)
  CreatedAt (DateTime)
  UpdatedAt (DateTime)
```

---

## MCP Tools (v1)

```
get_context(type?)     → zwraca wpisy użytkownika, opcjonalnie filtruje po typie
add_note(type, content) → dodaje nowy wpis do kontekstu
```

---

## Struktura repozytorium

```
lumen/
├── src/
│   ├── Lumen.Api/            # .NET Minimal APIs — HTTP endpoints
│   ├── Lumen.Application/    # Use cases, CQRS (Commands, Queries)
│   ├── Lumen.Domain/         # Encje, interfejsy repozytoriów
│   ├── Lumen.Infrastructure/ # EF Core, PostgreSQL, Clerk
│   └── Lumen.Mcp/            # MCP Server — osobny BC, tools dla AI
├── frontend/                 # (placeholder, v2)
├── docs/
│   ├── vision.md
│   ├── architecture.md
│   └── decisions/            # ADRy
├── docker-compose.yml
└── .github/
    └── workflows/
        └── ci.yml
```

---

## Zadania — v1

### 🏗️ Etap 1: Fundament (repo + lokalny dev)

- [ ] Utworzyć repo na GitHubie (`lumen`)
- [ ] Dodać `.gitignore` dla .NET + Node
- [ ] Utworzyć solucję .NET z projektami:
  - `Lumen.Domain`
  - `Lumen.Application`
  - `Lumen.Infrastructure`
  - `Lumen.Api`
  - `Lumen.Mcp`
- [ ] Skonfigurować `docker-compose.yml` z PostgreSQL
- [ ] Dodać `docs/vision.md` (przenieść z tego pliku)
- [ ] Dodać pierwszy ADR: `docs/decisions/001-monolith-vs-microservices.md`
- [ ] Conventional Commits od pierwszego commita (`feat:`, `fix:`, `docs:`)

---

### 🗄️ Etap 2: Baza danych + Domain

- [ ] Dodać EF Core + Npgsql do `Lumen.Infrastructure`
- [ ] Zdefiniować encje w `Lumen.Domain`:
  - `User`
  - `ContextEntry` (z `ContextEntryType` enum)
- [ ] Skonfigurować `DbContext` w `Lumen.Infrastructure`
- [ ] Dodać pierwszą migrację EF Core
- [ ] Zweryfikować że PostgreSQL lokalnie startuje z Docker Compose
- [ ] Dodać ADR: `docs/decisions/002-ef-core-vs-dapper.md`

---

### 🔐 Etap 3: Auth (Clerk)

- [ ] Założyć konto Clerk + aplikację
- [ ] Dodać middleware weryfikacji tokenów JWT w `Lumen.Api`
- [ ] Endpoint `POST /users/sync` — tworzy lub zwraca użytkownika po ClerkId
- [ ] Webhook Clerk → auto-sync przy pierwszym logowaniu
- [ ] Dodać ADR: `docs/decisions/003-clerk-vs-custom-auth.md`

---

### ✏️ Etap 4: CRUD kontekstu

- [ ] Command: `AddContextEntryCommand` (type, content)
- [ ] Command: `UpdateContextEntryCommand` (id, content)
- [ ] Command: `DeleteContextEntryCommand` (id)
- [ ] Query: `GetContextEntriesQuery` (opcjonalnie filtr po type)
- [ ] Query: `GetContextEntryQuery` (by id)
- [ ] Endpointy w `Lumen.Api`:
  - `GET /context` — lista wpisów
  - `GET /context/{id}` — pojedynczy wpis
  - `POST /context` — dodaj wpis
  - `PUT /context/{id}` — edytuj
  - `DELETE /context/{id}` — usuń
- [ ] Walidacja przez FluentValidation (content nie pusty, type poprawny)
- [ ] Testy integracyjne podstawowych endpointów

---

### 🤖 Etap 5: MCP Server

- [ ] Dodać pakiet MCP SDK do `Lumen.Mcp`
- [ ] Zaimplementować tool `get_context`:
  - parametr opcjonalny: `type` (fact | temporary | insight)
  - wywołuje `GetContextEntriesQuery` przez Application layer
  - zwraca sformatowaną listę wpisów
- [ ] Zaimplementować tool `add_note`:
  - parametry: `type`, `content`
  - wywołuje `AddContextEntryCommand`
  - zwraca potwierdzenie
- [ ] Przetestować połączenie z Claude Desktop
- [ ] Dodać ADR: `docs/decisions/004-mcp-as-separate-bc.md`

---

### ⚙️ Etap 6: CI/CD

- [ ] GitHub Actions — workflow `ci.yml`:
  - trigger: push na `main` + PR
  - kroki: restore → build → test
- [ ] Dodać badge CI do `README.md`
- [ ] README z opisem projektu, jak uruchomić lokalnie, jak podłączyć MCP

---

## Definicja "done" dla v1

```
✓ docker-compose up → PostgreSQL startuje
✓ API startuje lokalnie
✓ MCP Server startuje lokalnie
✓ W Claude Desktop: get_context() zwraca moje wpisy
✓ W Claude Desktop: add_note() dodaje wpis do bazy
✓ GitHub Actions: zielony pipeline na każdym PR
✓ README: ktoś obcy może uruchomić projekt lokalnie
```

---

## Kolejność nauki przy v1

```
Etap 1: ADR, Conventional Commits, struktura solucji .NET
Etap 2: EF Core, migracje, PostgreSQL, Domain modeling
Etap 3: JWT, middleware, Clerk integracja
Etap 4: CQRS w praktyce, MediatR, FluentValidation, testy integracyjne
Etap 5: MCP protokół, tool calling, bounded context w praktyce
Etap 6: GitHub Actions, CI pipeline jako kod
```

---

## Otwarte decyzje

- [ ] MediatR vs czysty CQRS bez biblioteki — do decyzji przy Etapie 4
- [ ] Jak autentykować MCP Server (czy MCP też weryfikuje token?)
