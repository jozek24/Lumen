# Lumen — dokument roboczy

> Aktualizowany iteracyjnie w trakcie planowania i budowania.

---

## Nazwa i wizja

**Lumen** *(łac. światło)* — widzisz siebie wyraźniej.

### Wizja w jednym akapicie
> Aplikacja która buduje coraz głębszy obraz Ciebie w czasie. Wie co mówisz że jest ważne, widzi co faktycznie robisz, łączy wzorce między domenami których sam nie widzisz. Rano daje Ci nie plan — ale pytanie i jedno zdanie kontekstu. Przez MCP zasila każde AI którego używasz. Z czasem staje się lustrem z pamięcią — pokazuje jak się zmieniasz jako osoba.

### Metafora produktu
Nie asystent (czeka na input). Nie coach (ocenia). Nie planer (lista zadań).

**Przyjaciel który czytał Twój dziennik przez 2 lata** — pamięta to co Ty zapominasz, mówi rzeczy których sam nie widzisz, nie owija w bawełnę.

---

## Problem który rozwiązuje

Każdy dziś przeskakuje między Claude, ChatGPT, Cursorem, notatkami, agentami — i każde AI ma osobną pamięć. Za każdym razem tłumaczysz kim jesteś, co robisz, czego chcesz.

Lumen to **osobista warstwa kontekstu, niezależna od modelu** — zasilająca każde AI przez MCP.

---

## Co jest inne niż konkurencja

| Narzędzie | Problem |
|---|---|
| Notion, Obsidian | Czekają na Ciebie. Wymagają porządku na wejściu. |
| ChatGPT memory | Zamknięta w ekosystemie. Nie działa w Cursorze. |
| Daylio, Habitica | Jedna domena. Nie widzą korelacji. |
| **Lumen** | Obserwuje, łączy wzorce, mówi to czego sam nie widzisz. Zasilą każde AI przez MCP. |

---

## Kluczowe możliwości (docelowo)

**Wersje siebie w czasie** — "kim byłem pół roku temu?" Portret z tamtego okresu. Realny obraz zmiany.

**Wykrywanie sprzeczności** — mówisz że gitara jest ważna, nie wspomniałeś o niej od 3 tygodni. Apka pyta, nie osądza.

**Korelacje między domenami** — Strava nie wie o Twoich celach zawodowych. Notion nie wie że wczoraj byłeś na siłowni. Lumen wie.

**Proaktywne rozszerzanie horyzontów** — nie czeka aż zapytasz. Podrzuca perspektywy których jeszcze nie rozważałeś.

**Poranny brief** — nie lista zadań. Pytanie i jeden zdanie kontekstu. "Masz jedno ważne zadanie które odkładasz od tygodnia. Co by się musiało stać żebyś powiedział że ten tydzień był udany?"

---

## Trzy warstwy kontekstu (decyzja architektoniczna)

```
Facts           → stałe fakty o mnie
                  "mam 31 lat, uczę się Reacta, gram na gitarze"

Temporary       → aktualny focus, zmienia się w czasie
                  "skupiam się na gitarze przez miesiąc, robię projekt MCP"

Insights        → wzorce które AI odkrywa w czasie
                  "najlepiej uczę się iteracyjnie, porzucam przy dużym scope"
```

---

## Decyzje wizji

| Pytanie | Decyzja |
|---|---|
| Głębokość kontekstu | Zaczynamy od faktów i celów. Warstwa emocjonalna dochodzi gdy mamy dane i wiemy co jest użyteczne. |
| Dla kogo | Na razie tylko dla Ciebie. `user_id` na każdym rekordzie od dnia 1 — nie blokuje multi-user w przyszłości. |
| Interfejs | Chat tekstowy jako MVP. Głos jako v-coś gdy core działa. |

---

## Iteracyjna roadmapa

```
v1: Context core + MCP server
    → dodajesz kontekst o sobie (Facts, Temporary, Insights)
    → MCP server wystawia go na zewnątrz
    → Claude Desktop / Cursor zna Ciebie
    → podstawowy chat z AI nad Twoim kontekstem

v2: Obserwacja w czasie
    → AI aktywnie aktualizuje kontekst przez rozmowę
    → poranny brief — pytanie + zdanie kontekstu
    → historia wpisów

v3: Wzorce i korelacje
    → "od 3 tygodni nie ćwiczysz gitary"
    → wersje siebie w czasie — porównanie miesięcy

v4: Rozszerzanie horyzontów
    → proaktywne podrzucanie perspektyw
    → rekomendacje oparte na wzorcach

v5: Multi-user / udostępnienie innym
    → dopiero tu dostosowujemy architekturę do produktu
```

---

## Stack

### Frontend
| Tech | Decyzja |
|---|---|
| Framework | React + Vite + TypeScript |
| UI | Tailwind + shadcn/ui |
| Data fetching | TanStack Query |
| State | Zustand |

### Backend
| Tech | Decyzja |
|---|---|
| Runtime | .NET 9 |
| Styl API | Minimal APIs |
| Architektura | Clean Architecture |

### Baza danych
| Tech | Decyzja |
|---|---|
| Główna | PostgreSQL |
| Rozszerzenie | pgvector (semantic search, embeddingi) |

### AI
| Tech | Rola |
|---|---|
| Microsoft.Extensions.AI | Abstrakcja nad modelem |
| MCP Server (własny) | Wystawiany z .NET — Claude Desktop, Cursor |
| MCP Client (własny) | Agent korzysta z zewnętrznych MCP serverów |
| SignalR | Streaming AI do frontendu |

### Auth + Infra
| Tech | Decyzja |
|---|---|
| Auth | Clerk lub Auth0 |
| Repo | GitHub |
| CI/CD | GitHub Actions |
| Lokalny dev | Docker Compose |
| Deploy | Railway / Render / Fly.io |

---

## Architektura (szkic)

```
┌─────────────────────────────────────────┐
│          React + Vite (Frontend)         │
│  Dashboard | Edytor kontekstu | Chat AI  │
└──────────────────┬──────────────────────┘
                   │ REST + SignalR
┌──────────────────▼──────────────────────┐
│              .NET 9 Backend              │
│       Clean Architecture + Minimal APIs  │
│         Microsoft.Extensions.AI          │
└──────┬──────────────────┬───────────────┘
       │                  │
┌──────▼──────┐    ┌──────▼──────────────┐
│ PostgreSQL  │    │     MCP Server       │
│ + pgvector  │    │  (bounded context)   │
└─────────────┘    │                      │
                   │  get_context()       │
                   │  search_context()    │
                   │  add_note()          │
                   │  update_focus()      │
                   │  log_insight()       │
                   └──────────────────────┘
                          │
         ┌────────────────┼────────────────┐
         ▼                ▼                ▼
   Claude Desktop       Cursor         Lumen app
```

---

## Struktura repozytorium (planowana)

```
lumen/
├── src/
│   ├── Lumen.Api/           # .NET Minimal APIs
│   ├── Lumen.Application/   # Use cases, CQRS
│   ├── Lumen.Domain/        # Encje, logika domenowa
│   ├── Lumen.Infrastructure/# DB, AI, MCP
│   └── Lumen.Mcp/           # MCP Server (osobny bounded context)
├── frontend/                # React + Vite
├── docs/
│   ├── vision.md
│   ├── architecture.md
│   └── decisions/           # ADRy — co wybrałeś i dlaczego
├── docker-compose.yml
└── .github/workflows/       # GitHub Actions
```

---

## Czego się Józef nauczy

- React głębiej — hooki, patterns, composable components
- TanStack Query — caching, invalidation
- PostgreSQL + pgvector — embeddingi, semantic search
- Docker Compose — multi-service dev environment
- GitHub Actions — CI/CD
- SignalR — streaming AI responses
- Microsoft.Extensions.AI — własna architektura
- MCP Server + Client od zera — wyróżnik w portfolio
- ADRy — architektoniczne myślenie seniorowe
- Iteracyjne budowanie produktu

---

## Do ustalenia — następny krok

- [ ] Planowanie v1 konkretnie — bounded contexts, encje, pierwsze zadania
- [ ] Wybór między EF Core a Dapper
- [ ] Konkretny model AI na start (OpenAI / Anthropic)
- [ ] Clerk vs Auth0

---

## Notatki

- `user_id` na każdym rekordzie od dnia 1 — nie blokuje multi-user w przyszłości
- MCP jako osobny bounded context — nie mieszamy domain logic z transport layer
- `docs/decisions/` od pierwszego dnia — ADRy robią wrażenie portfolio-wise
- Frontend pisany z pomocą AI — focus na architekturze i AI features
- ADO zostaje w CV jako enterprise skill — GitHub Actions to nowa pozycja
