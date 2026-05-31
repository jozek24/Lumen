# Lumen — historia projektu

> Chronologiczny log decyzji, nauki i postępów.
> Aktualizowany co sesję.

---

## Sesja 1 — Planowanie

### Ustalony kierunek i stack
- Cel: projekt fullstack do nauki + portfolio + użytek własny
- Backend: .NET 9, Clean Architecture, Minimal APIs
- Frontend: React + Vite + TypeScript, Tailwind, shadcn/ui, TanStack Query, Zustand
- Baza: PostgreSQL + pgvector (semantic search w v3)
- AI: Microsoft.Extensions.AI, MCP Server + Client
- Auth: Clerk (darmowy do 50k użytkowników, bez karty)
- Infra: GitHub + GitHub Actions, Docker Compose, Railway/Render
- ADO zostaje w CV — GitHub Actions to nowa umiejętność

### Pomysł na projekt
- Iterowaliśmy przez wiele pomysłów:
  - News aggregator → odrzucony (LLM już to robi)
  - ADHD planner → odrzucony (nieoryginalny, ADHD nie ujawniamy w portfolio)
  - Gitara + AI nauczyciel → odrzucony (jamowanie z AI nie to czego szukał Józef)
  - Mindfulness / MBSR → odrzucony (silna konkurencja)
  - Personal Intelligence OS → odrzucony (zbyt generyczna nazwa)
- Doszliśmy do: **Personal Context Server** — warstwa kontekstu zasilająca każde AI przez MCP
- Kluczowy insight: MCP jest nowy (koniec 2024), ekosystem osobistych serwerów praktycznie nie istnieje — wchodzisz wcześnie
- AION MIND (aionmind.com) to podobna apka mobilna — ale Lumen jest fundamentalnie inny przez MCP i otwartość

### Nazwa
- Wybrana nazwa: **Lumen** *(łac. światło — widzisz siebie wyraźniej)*
- Odrzucone: Personal Intelligence OS (zbyt generyczne), Mirror, Meridian, Opus, Chronicle

### Wizja docelowa
- Lustro z pamięcią — obserwuje Cię w czasie, mówi rzeczy których sam nie widzisz
- Trzy warstwy kontekstu: Facts / Temporary / Insights
- MCP bidirectional: AI może czytać i zapisywać kontekst
- Poranny brief (pytanie + zdanie kontekstu, nie lista zadań)
- Wykrywanie sprzeczności ("mówiłeś że gitara jest ważna, nie grałeś 3 tygodnie")
- Korelacje między domenami
- Wersje siebie w czasie ("kim byłem pół roku temu?")

### Bounded Contexts
- **Context** — rdzeń: Facts, Temporary, Insights
- **Conversation** — historia rozmów z AI (osobny BC — inna odpowiedzialność)
- **Mcp** — osobny BC, transport layer (nie miesza się z domain logic)
- User — nie BC, cienka encja z ClerkId jako referencją

### Encje w Domenie
Dwie encje:
- **User** — Id (Guid), ClerkId (string), CreatedAt
- **ContextEntry** — Id (Guid), UserId (Guid), Type (enum: Fact|Temporary|Insight), Content (string), CreatedAt, UpdatedAt

Decyzja: Facts/Temporary/Insights to nie osobne encje — to typy jednej encji ContextEntry.
Uzasadnienie: jedna tabela, łatwe rozszerzanie typów, AI operuje na wszystkich wpisach jednakowo.

### Roadmapa
- v1: Context CRUD + MCP Server → Claude Desktop zna Ciebie
- v2: Frontend React + AI chat nad kontekstem
- v3: Embeddingi + pgvector + semantic search + deploy ⭐
- v4+: Pattern recognition, poranny brief, wykrywanie sprzeczności
- v5: Multi-user / udostępnienie innym

### Decyzje architektoniczne
- **Monolith na start** — jeden projekt .NET, foldery według warstw. Dyscyplina przez Clean Architecture ważniejsza niż podział na mikroserwisy.
- **user_id na każdym rekordzie od dnia 1** — nie blokuje multi-user w v5
- **MCP jako osobny bounded context** — nie miesza domain logic z transport layer. Osobny projekt bo inna odpowiedzialność = inny powód do zmiany.
- **MCP nie rozmawia z bazą bezpośrednio** — zawsze przez Application layer (use cases → Infrastructure → DB)
- **docs/decisions/** od dnia 1 — ADRy (Architecture Decision Records)
- **Conventional Commits** od pierwszego commita (feat:, fix:, docs:, refactor:)
- **GitHub Actions nie ADO** — natywna integracja z repo, widoczne w portfolio, nowa pozycja w CV
- **Clerk nie custom auth** — nie tracimy czasu w v1; custom auth jako osobny projekt do nauki
- **React + Vite nie Next.js** — Next.js bez sensu przy osobnym backendzie .NET
- **Nie używamy Aspire** — rozwiązuje problemy których Lumen nie ma; Docker Compose wystarczy
- **Nie używamy szablonów VS** — CLI daje pełną kontrolę i zrozumienie struktury

### Zakres v1
✅ W v1:
- GitHub repo + struktura projektu
- Docker Compose (PostgreSQL lokalnie)
- Backend — Clean Architecture, warstwy, encje
- Auth — Clerk
- CRUD kontekstu (Facts / Temporary / Insights)
- MCP Server (get_context, add_note)
- GitHub Actions (podstawowy pipeline)

⏳ v2: Frontend React + AI chat
⏳ v3: Embeddingi + pgvector + semantic search + deploy
⏳ Osobny projekt: Custom auth (JWT od zera, refresh tokeny, OAuth)
⏳ Osobny projekt: Gitara + AI nauczyciel

### Czego się uczymy
- Plik nauka.md zawiera pełną listę z priorytetami
- Zasada: najpierw Józef próbuje odpowiedzieć, potem komentarz
- Trzy typy ćwiczeń: "Spróbuj sam", "Co by się stało gdyby", "Uzasadnij"

### Stan na koniec sesji
- ✅ Repo utworzone na GitHubie (Lumen, publiczne, MIT, .gitignore Dotnet)
- ✅ Description: "Personal context server built with .NET and MCP"
- ⏳ Solucja .NET — następny krok (przez CLI, nie VS)
- ⏳ Docker Compose
- ⏳ Pierwszy ADR

---

## Następna sesja — jak zacząć

Wklej na początku rozmowy:
```
szkic-infrastruktury.md
v1-plan.md
nauka.md
historia.md
```

Powiedz: "Pracujemy nad Lumenem. Przeczytaj pliki i powiedz gdzie skończyliśmy."

Następny konkretny krok: **Etap 1 — struktura solucji .NET przez CLI**

```bash
dotnet new sln -n Lumen
dotnet new classlib -n Lumen.Domain -o src/Lumen.Domain
dotnet new classlib -n Lumen.Application -o src/Lumen.Application
dotnet new classlib -n Lumen.Infrastructure -o src/Lumen.Infrastructure
dotnet new webapi -n Lumen.Api -o src/Lumen.Api
dotnet new classlib -n Lumen.Mcp -o src/Lumen.Mcp
dotnet sln add src/Lumen.Domain
dotnet sln add src/Lumen.Application
dotnet sln add src/Lumen.Infrastructure
dotnet sln add src/Lumen.Api
dotnet sln add src/Lumen.Mcp
```

### Narzędzie do następnych sesji
Rozważamy Claude Code — widzi pliki bezpośrednio, nie traci kontekstu między sesjami.
Zasada: zanim wpiszesz komendę — najpierw powiedz co chcesz zrobić i dlaczego.
