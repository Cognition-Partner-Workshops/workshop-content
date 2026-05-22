# Events

Event-specific workshop instances. Each event links to one or more workshops and adds event-specific details like agenda, repo list, and any customizations for that session.

## Finding Your Event

If your facilitator gave you an event link, go directly there. Otherwise, check `active/` for upcoming events or `archive/` for past ones.

```
events/
├── active/              ← upcoming or in-progress events
│   └── YYYY-MM-DD-<event-id>/
│       └── README.md
└── archive/             ← past events
    └── YYYY-MM-DD-<event-id>/
        └── README.md
```

## Looking for Labs?

Event READMEs contain your agenda and any event-specific customizations. The actual lab instructions live in:

- [`workshops/`](../workshops/) — structured lab sequences
- [`modules/`](../modules/) — individual lab instructions
