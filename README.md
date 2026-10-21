# Newsletter with scheduled sends

A newsletter that one person writes and nine thousand people read. The
writer writes an issue now and sets the minute it goes out. Every
subscriber gets one copy, however many lists they are on. Anybody who
has left gets nothing more. If the app restarts in the middle of a
send, the people who already had the issue do not get it again.

## What finished looks like

Anyone can open the app and:

- put their email address on a list, and confirm that it is theirs
- take themselves off again with one click, with no password

The writer signs in, writes an issue and schedules it. At the minute
they chose, with nobody at the keyboard, it goes to everybody still on
the list:

- one copy each, however many lists they are on
- no second copy for anybody, even if a restart interrupts the send
  halfway
- nothing more, ever, to an address that has stopped accepting mail

Finished means it is live on the internet, not just on your laptop.
The issue goes out at nine on Tuesday while the writer is asleep.

## The road map

Eight sprints. Each sprint delivers one part of the list above. They
must be done in order, because each one builds on the one before.

| # | Sprint | What it delivers |
|---|---|---|
| 1 | A desk of your own | The newsletter exists, one writer owns it, and they can write an issue |
| 2 | The list | People join, and nothing is sent to an address until its owner confirms it |
| 3 | Leaving | Readers leave with one click, no password, for good |
| 4 | Nine thousand, once each | A send makes one copy per person, so somebody on three lists gets one copy |
| 5 | Nine on Tuesday | An issue waits until the minute the writer chose, and goes out as they last left it |
| 6 | Nobody at the keyboard | Issues go out by themselves, are sent once, and finish after a restart |
| 7 | Addresses that stopped working | Dead addresses stop being written to, and the writer sees what a send did |
| 8 | Out where people are | The app is live on the internet, and keeps sending while the laptop is shut |

## The hard part

Sending exactly once, nine thousand times over.

A worker (a background process that does the sending) sends four
thousand copies, and then the server restarts. The job queue does not
know how far the worker got, so it hands out the same work again. If
nothing stops it, four thousand people get the same issue twice. People
unsubscribe over that, and tell each other about it. Sprints 4 and 6
solve this. The fix is not a longer timeout. It is a record of every
copy, kept where the send can find it again.

The other hard part needs almost no code. Leaving must always work: one
click, no sign-in, from an email somebody opens a year after it
arrived. Without that, readers press "Report spam" instead, and the
newsletter stops reaching everybody.

## Working on it

The sprints, their briefs and their tickets are in Blacksmith
Experience. Start with sprint 1. Each sprint opens when the one before
it is finished.

Read the documents attached to a sprint before you start its first
ticket. They explain the choices you will make and the options you are
choosing between.

## What this is built with

The whole product: an API and the screens that use it.

- **Django** with **Django REST Framework** for the API.
- **SimpleJWT** for authentication, against a custom user model in `apps/users`.
- **drf-spectacular** for the OpenAPI schema, served at `/api/schema/` and browsable at `/api/docs/`.
- **React** with **Vite** for the dev server and the build.
- **Chakra UI** for components, and **React Router** for routes.
- **TanStack Query** for every call to the API, so caching and refetching are decided in one place.

## Setting it up

You need the CLI once: `npm install -g blacksmith-cli`.

```bash
blacksmith setup     # dependencies, database, migrations
blacksmith dev       # start it
```

The API answers on `http://localhost:8000`, and the app on `http://localhost:5173`.

Copy `backend/.env.example` to `backend/.env` before the first run. It is ignored by git and holds the secret key, the database URL and anything else this project should not carry in its history.

## Where the code lives

```
backend/
├── config/
│   ├── settings/        # base, development, production
│   └── urls.py          # where routes are mounted
├── apps/
│   └── users/           # the custom user model, and auth
├── utils/               # shared helpers, base model
├── manage.py
└── requirements.txt

frontend/
└── src/
    ├── api/
    │   ├── generated/   # written by `blacksmith sync` — do not edit
    │   └── hooks/       # your queries and mutations
    ├── pages/           # one folder per page
    ├── features/        # auth, and anything else that spans pages
    ├── router/          # routes and layouts
    ├── shared/          # components and hooks used across pages
    └── styles/
```

## Day to day

| Command | What it does |
| --- | --- |
| `blacksmith dev` | Run it locally. |
| `blacksmith sync` | Regenerate the frontend API types and hooks from the backend schema. Run it after changing a serializer or a route. |
| `blacksmith make:resource Post` | Scaffold a model, serializer, viewset and routes, plus the hooks and pages that use them. |
| `blacksmith backend <command>` | Run a Django management command, e.g. `blacksmith backend createsuperuser`. |
| `blacksmith frontend <command>` | Run an npm command in the frontend, e.g. `blacksmith frontend install axios`. |
| `blacksmith eject` | Remove Blacksmith and keep a plain Django and React project. Nothing here is a dependency on us. |
