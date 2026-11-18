# Software Architecture & Design: Online Bookstore

**CEN 4065 – Software Architecture & Design (Fall 2026)** · Dr. Buckley · Group 6

**Team:** Vanessa Gutierrez, Roy Batuman, Camilo Palacio Rivero

Important Remarks:

- Per user stories, this is to be a GENERAL bookstore system (NOT a college/university bookstore)

This repository holds our team's design work for an online bookstore system built from 18 user stories covering two roles: **customers** (search, cart, checkout, order history, returns, e-book downloads) and **administrators** (book and inventory management, return approval, database backup/restore).

Each team member kept their own folder of working diagrams, and the agreed group deliverables are in `Group Work/`.

## Repository structure

```
.
├── Group Work/        Group 6 deliverables for Assignments 2, 3 and 4
├── Vanessa/           Vanessa's individual diagrams
├── Roy/               Roy's individual diagrams
├── Camilo/            Camilo's individual diagrams
├── Resources/         Course and research reference PDFs
└── References/        Citations used for the project
```

### `Group Work/`: group deliverables

| Assignment | Files | What it shows |
|---|---|---|
| **A2 – Class diagram** | `Assignment2.drawio`, `Assignment2.jpg` | UML class diagram: classes, attributes, operations and relationships, grouped into modules (User, Catalog, Cart, Order, Return, Payment, Inventory, Backup) |
| **A3 – Sequence diagram** | `Assignment3.drawio`, `Assignment3.jpg` | UML sequence diagram of the e-book purchase flow: search → add to cart → checkout → payment → download link |
| **A4 – High-level architecture** | `Assignment4_HLA_group6.drawio`, `Assignment4_HLA_group6.jpg` | Layered service-oriented architecture (SOA) diagram |

The `.drawio` files are editable in [draw.io / diagrams.net](https://app.diagrams.net). The `.jpg` and `.png` files are exported images of the same diagrams.

**Architecture at a glance (A4):**

| Layer | Components |
|---|---|
| Presentation | Storefront, Admin Console |
| Integration | API Gateway, Enterprise Service Bus (ESB) |
| Business services | Account, Catalog, Inventory, Backup, Return, Cart, Order, Payment |
| Data access | Data Access Layer |
| Data | Bookstore Database, E-Book File Store, Backup Store |
| External | Stripe API, reached through a payment adapter |

**Design patterns used in the class diagram:** State (`OrderState`, `ReturnState`), Factory (`PaymentFactory`), Adapter (`StripeGatewayAdapter` / `PaymentGateway`), Proxy (`SecureAccountProxy`), Observer (`Inventory` → `LowStockNotifier`), Singleton (`Inventory`) and Memento (`Database` / `DatabaseMemento`).

### `Vanessa/`

| File | Description |
|---|---|
| `UML Class Diagram.drawio` / `.png` | Vanessa's class diagram (draw.io source and image) |
| `UML Sequence Diagram.drawio` / `.jpg` | Vanessa's sequence diagram (draw.io source and image) |
| `HLA Diagram.png` | Vanessa's high-level architecture diagram |
| `Ideas for Increments` | Notes on planned improvements (OrderState/ReturnState interfaces, Observer pattern for Inventory, Adapter pattern for payments, re-verifying the patterns) |

### `Roy/`

| File | Description |
|---|---|
| `bookstoreclass.drawio`, `bookstore.drawio.png` | Class diagram |
| `ebook_sequence_diagram.drawio`, `Ebook Sequence diagram.png` | E-book purchase sequence diagram |
| `highleveldiagram.drawio`, `highleveldiagram.drawio.png` | High-level architecture diagram |

### `Camilo/`

| File | Description |
|---|---|
| `class-diagram.md` | Class diagram (Mermaid) |
| `sequence-diagram.md` | Sequence diagram (Mermaid) |
| `bookstore-use-case-diagram.png` | Use case diagram |
| `soa-diagram.png` | SOA diagram |

### `Resources/` and `References/`

| Path | Description |
|---|---|
| `Resources/Architecture Checklist.pdf` | Architecture checklist |
| `Resources/Architecture and Domain Research Process.pdf` | Research process guide |
| `Resources/Design Pattern Recognition.pdf` | Design pattern recognition reference |
| `Resources/Design_Rationale.pdf` | Design rationale reference |
| `Resources/Design Patterns` | Link to [refactoring.guru/design-patterns](https://refactoring.guru/design-patterns) |
| `References/References` | Project references |
| `References/IEEE Reference Guide` | IEEE citation guide |

## Viewing the diagrams

- **`.drawio` files:** open them at [app.diagrams.net](https://app.diagrams.net) (File → Open from → Device), or with the draw.io desktop app or VS Code extension.
- **Mermaid files (`Camilo/*.md`):** GitHub renders them directly in the browser.
