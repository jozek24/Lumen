# Lumen — CLAUDE.md

Personal context server built with .NET and MCP.

---

## Uruchamianie lokalne

```bash
# Baza danych
docker-compose up -d

# Backend
dotnet run --project src/Lumen.Api

# MCP Server
dotnet run --project src/Lumen.Mcp
```

## Budowanie i testy

```bash
dotnet build
dotnet test
```

## Migracje EF Core

```bash
dotnet ef migrations add <NazwaMigracji> --project src/Lumen.Infrastructure --startup-project src/Lumen.Api
dotnet ef database update --project src/Lumen.Infrastructure --startup-project src/Lumen.Api
```

---

## Struktura projektu

```
src/
├── Lumen.Domain/         # Encje, interfejsy repozytoriów — zero zależności zewnętrznych
├── Lumen.Application/    # Use cases, Commands, Queries, MediatR
├── Lumen.Infrastructure/ # EF Core, PostgreSQL, Clerk — implementacje interfejsów z Domain
├── Lumen.Api/            # Minimal APIs — tylko routing i mapowanie na Commands/Queries
└── Lumen.Mcp/            # MCP Server — osobny Bounded Context, nie dotyka bazy bezpośrednio
```

---

## Zasady architektoniczne

- Zależności tylko do środka: `Api → Application → Domain`. Infrastructure implementuje interfejsy z Domain.
- `Lumen.Mcp` komunikuje się wyłącznie przez Application layer — nigdy bezpośrednio z bazą.
- Jeden Command/Query = jedna klasa, jeden handler.
- `user_id` na każdym rekordzie.

## Konwencje kodu

- Conventional Commits: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`
- Każda zmiana na osobnym branchu, merge przez PR.
- Walidacja requestów przez FluentValidation, nie w handlerach.
- ADRy dla każdej istotnej decyzji architektonicznej: `docs/decisions/NNN-nazwa.md`

## Czego nie robić

- Nie używaj szablonów Visual Studio — tylko CLI (`dotnet new`).
- Nie pisz komentarzy w kodzie — nazwy metod i klas mają być wystarczające.
- Nie dodawaj logiki do `Lumen.Api` — tylko routing i mapowanie.
- Nie wywołuj bazy bezpośrednio z `Lumen.Mcp`.
- Nie używaj wyjątków do flow control — Result pattern.
