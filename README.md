# Space Shooter

Ein Top-down Arcade Space Shooter, gebaut mit **Godot 4.7** und GDScript.
Persönliches Lernprojekt — Ziel ist ein kleines, aber fertiges Spiel.

## Setup

1. [Godot 4.7.2](https://godotengine.org/download) installieren (Standard-Version, nicht .NET)
2. Repo klonen:
   ```bash
   git clone https://github.com/dasHund1982/godot_spaceshooter.git
   ```
3. In Godot: **Import** → `project.godot` auswählen

## Steuerung

| Taste | Aktion |
|---|---|
| WASD / Pfeiltasten | Bewegen |
| Leertaste | Schießen |
| Esc | Pause |

## Status

**Aktueller Meilenstein:** M0 — Skelett

Die vollständige Roadmap steht in [`docs/ROADMAP.md`](docs/ROADMAP.md).

## Projektstruktur

```
scenes/     Szenen, jeweils mit ihrem Script daneben
autoload/   Singletons (GameState, EventBus, Audio)
resources/  Custom Resources (Gegner-Stats, Wellen)
assets/     Grafik, Sound, Fonts
docs/       Roadmap, Entscheidungen, Lerntagebuch
```

## Lizenz

Noch nicht festgelegt.
