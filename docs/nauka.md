# Lumen — czego się uczę

> Plik roboczy. Odhaczamy w trakcie budowania projektu.
> Każdy temat pojawi się naturalnie przy konkretnym zadaniu — nie uczymy się w próżni.

---

## Zasady nauki w tym projekcie

**Najpierw Ty, potem ja** — przy każdej decyzji architektonicznej najpierw Józef próbuje odpowiedzieć, potem ja komentuję. Nie ma złych odpowiedzi — jest myślenie.

Trzy typy ćwiczeń:
- **"Spróbuj sam"** — zanim powiem jak coś zrobić, Ty próbujesz
- **"Co by się stało gdyby"** — pokazuję konsekwencje złej decyzji
- **"Uzasadnij"** — nie pytam co wybrałeś, tylko dlaczego

---

## Legenda
- [ ] do zrobienia
- [~] w trakcie / poznałem podstawy
- [x] opanowane

---

## Architektura i wzorce projektowe

### Domain-Driven Design (DDD)
- [ ] **Bounded Context (BC)** — granica wokół kawałka systemu z własną odpowiedzialnością i słownikiem. *Poznajemy przy: projektowaniu struktury Lumena*
- [ ] **Aggregate** — grupa obiektów traktowana jako jedna całość. Jeden punkt wejścia do zmiany stanu. *Poznajemy przy: modelowaniu domeny Context*
- [ ] **Entity** — obiekt z tożsamością (ma Id, zmienia się w czasie). *Poznajemy przy: pierwszych encjach*
- [ ] **Value Object** — obiekt bez tożsamości, definiowany przez wartość (np. Email, Money). *Poznajemy przy: modelowaniu Facts/Insights*
- [ ] **Domain Event** — coś co się wydarzyło w domenie (np. ContextUpdated). *Poznajemy przy: komunikacji między BC*
- [ ] **Repository** — abstrakcja dostępu do danych. Domena nie zna bazy. *Poznajemy przy: warstwie Infrastructure*

### Clean Architecture
- [ ] **Warstwy** — Domain → Application → Infrastructure → API. Zależności idą tylko do środka. *Poznajemy przy: tworzeniu struktury projektu*
- [ ] **Use Case / Command / Query** — jedna klasa = jedna operacja. *Poznajemy przy: pierwszych endpointach*
- [ ] **CQRS** — rozdzielenie odczytu (Query) od zapisu (Command). *Poznajemy przy: projektowaniu API*
- [ ] **Dependency Inversion** — zależysz od abstrakcji, nie implementacji. *Poznajemy przy: rejestracji serwisów*

### Ogólne wzorce
- [ ] **ADR (Architecture Decision Record)** — zapisujesz co wybrałeś, co odrzuciłeś i dlaczego. *Stosujemy od dnia 1*
- [ ] **Vertical Slice Architecture** — alternatywa dla layered, organizujesz kod wokół ficzerów nie warstw. *Poznajemy przy: dyskusji o strukturze folderów*

---

## Backend — .NET

- [ ] **Minimal APIs** — lżejsza alternatywa dla Controllers w .NET 9. *Poznajemy przy: pierwszym endpoincie*
- [ ] **MediatR** — biblioteka do CQRS, decoupluje handlery od endpointów. *Poznajemy przy: warstwie Application*
- [ ] **EF Core** — ORM do PostgreSQL. Migracje, DbContext, konfiguracja encji. *Poznajemy przy: pierwszej migracji*
- [ ] **FluentValidation** — walidacja requestów. *Poznajemy przy: pierwszym command*
- [ ] **Result pattern** — zamiast wyjątków, metody zwracają Result<T>. Czystszy error handling. *Poznajemy przy: use cases*
- [ ] **Options pattern** — konfiguracja przez strongly-typed klasy zamiast magic strings. *Poznajemy przy: konfiguracji AI i bazy*
- [ ] **Middleware** — pipeline requestów HTTP, error handling, logowanie. *Poznajemy przy: konfiguracji API*

---

## Baza danych

- [ ] **PostgreSQL** — różnice vs SQL Server, typy danych, JSON support. *Poznajemy przy: pierwszej migracji*
- [ ] **pgvector** — rozszerzenie do przechowywania wektorów (embeddingów). *Poznajemy przy: semantic search*
- [ ] **Embeddingi** — zamiana tekstu na wektor liczb. Podobne teksty mają podobne wektory. *Poznajemy przy: Context search (v3)*
- [ ] **Semantic search** — szukasz po znaczeniu, nie po słowach kluczowych. *Poznajemy przy: MCP tool search_context (v3)* ⭐ priorytet
- [ ] **Migracje** — zarządzanie zmianami schematu bazy przez kod. *Poznajemy przy: EF Core setup*

---

## AI i MCP

- [ ] **Microsoft.Extensions.AI** — abstrakcja nad modelami AI w .NET. Łatwa zamiana OpenAI ↔ Anthropic ↔ Ollama. *Poznajemy przy: pierwszym chacie*
- [ ] **MCP (Model Context Protocol)** — protokół wymiany kontekstu między AI a narzędziami. *Poznajemy przy: Lumen.Mcp*
- [ ] **MCP Server** — wystawiasz tools które AI może wywołać. *Poznajemy przy: get_context, search_context*
- [ ] **MCP Client** — Twoja apka woła zewnętrzne MCP servery. *Poznajemy w późniejszych iteracjach*
- [ ] **Tool calling / Function calling** — AI decyduje kiedy wywołać Twój kod. *Poznajemy przy: pierwszym tool*
- [ ] **Streaming** — AI odpowiada token po tokenie zamiast czekać na całość. *Poznajemy przy: SignalR + chat*
- [ ] **Prompt engineering** — jak pisać prompty żeby AI dawał przewidywalne odpowiedzi. *Poznajemy przy: każdym AI feature*
- [ ] **RAG (Retrieval Augmented Generation)** — AI odpowiada na podstawie Twoich danych, nie tylko treningu. *Poznajemy przy: chat nad kontekstem*
- [ ] **Context window** — ile tekstu AI "widzi" naraz. Zarządzanie kontekstem w długich rozmowach. *Poznajemy przy: Conversation*

---

## Frontend — React

- [ ] **Hooki** — useState, useEffect, useCallback, useMemo, useRef. Kiedy i dlaczego. *Poznajemy przy: pierwszych komponentach*
- [ ] **Custom hooks** — wydzielanie logiki do re-używalnych hooków. *Poznajemy przy: refactoringu*
- [ ] **TanStack Query** — fetching, caching, invalidation, optimistic updates. *Poznajemy przy: pierwszym API call*
- [ ] **Zustand** — prosty state management. *Poznajemy przy: globalnym stanie (user, sesja)*
- [ ] **shadcn/ui** — komponenty UI które kopiujesz do projektu i modyfikujesz. *Poznajemy przy: setup frontendu*
- [ ] **React Router** — routing w SPA. *Poznajemy przy: strukturze stron*
- [ ] **Controlled vs Uncontrolled components** — jak React zarządza formularzami. *Poznajemy przy: edytorze kontekstu*

---

## Infra i DevOps

- [ ] **Docker** — konteneryzacja, Dockerfile, image vs container. *Poznajemy przy: pierwszym uruchomieniu lokalnym*
- [ ] **Docker Compose** — multi-service setup lokalnie (API + PostgreSQL). *Poznajemy przy: dev environment*
- [ ] **GitHub Actions** — CI/CD pipelines jako kod. *Poznajemy przy: pierwszym pipeline*
- [ ] **Environment variables i Secrets** — konfiguracja wrażliwych danych. *Poznajemy przy: konfiguracji lokalnej i CI*
- [ ] **Health checks** — endpoint który mówi czy apka żyje. *Poznajemy przy: deploy*

---

## Praktyki i narzędzia

- [ ] **ADR** — Architecture Decision Records. Piszemy od dnia 1. *docs/decisions/*
- [ ] **Conventional Commits** — format commitów: `feat:`, `fix:`, `docs:`, `refactor:`. *Od pierwszego commita*
- [ ] **Feature branches + PR** — każda zmiana na osobnym branchu, merge przez Pull Request. *Od początku*

---

## Kolejność nauki (orientacyjna)

```
Teraz (przy planowaniu):
  DDD podstawy → Clean Architecture → ADR

v1 setup:
  Docker Compose → PostgreSQL → Minimal APIs → EF Core

v1 core:
  Bounded Contexts w praktyce → CQRS → MediatR → Embeddingi

v1 AI:
  M.Extensions.AI → MCP Server → Tool calling → Streaming

v1 frontend:
  React hooki → TanStack Query → shadcn/ui → Zustand

v2+:
  RAG → MCP Client → Prompt engineering głębiej → pgvector zaawansowany
```

---

## Do nauki — osobne projekty (po Lumenie)

- [ ] **Custom auth od zera** — JWT, refresh tokeny, OAuth flow. Osobne repo, tydzień-dwa. Żeby wiedzieć co Clerk robi za Ciebie.
- [ ] **ASP.NET Core Aspire** — cloud-native orkiestracja, service discovery. Popularne w enterprise .NET. Nie potrzebne w Lumenie, ale warto znać.
- [ ] **Next.js** — SSR, SSG, API routes. Jeśli wymagane rekrutacyjnie — tydzień po opanowaniu React.
