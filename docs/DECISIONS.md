# Architekturentscheidungen

Kurzformat: **Was** · **Warum** · **Verworfen weil**.
Drei Zeilen pro Eintrag reichen. Das Ziel ist, in sechs Monaten
nicht mehr rätseln zu müssen, warum etwas so gebaut ist.

---

## 2026-09-18 — Godot 4.7 statt 4.6 oder 3.6

**Was:** Projekt läuft auf Godot 4.7.x, fest verdrahtet bis mindestens M6.
**Warum:** Aktuelle stabile Reihe, beste Doku- und Tutoriallage für 2D.
**Verworfen:** 3.6 LTS — alte GDScript-Syntax, Wissen wäre nicht übertragbar.

## 2026-09-18 — Repo flach, project.godot in der Wurzel

**Was:** Kein `project/`-Unterordner.
**Warum:** Jedes Tutorial, jede CI-Action und die Godot-Doku gehen davon aus.
**Verworfen:** Verschachtelte Variante — sauberere Trennung, aber ständige Reibung.

## 2026-09-18 — Git außerhalb von Godot

**Was:** Versionierung über VS Code / Kommandozeile, kein Godot-Git-Plugin.
**Warum:** Plugin hinkt Godot-Versionen hinterher; Git soll bewusst gelernt werden.
**Verworfen:** Godot Git Plugin, GitHub Desktop.

## 2026-09-18 — Art Direction: HD mit Neon-Glow, kein Pixel-Art

**Was:** 1920x1080, glatte HD-Sprites, WorldEnvironment-Glow. Referenz: Starforged Legacy (nur Optik).
**Warum:** Glow verzeiht fehlendes Zeichentalent; schlechte Pixel-Art faellt sofort auf.
**Verworfen:** Pixel-Art 640x360 — haetter jedes Sprite zur Einzelentscheidung gemacht.

## 2026-09-18 — Forward Plus statt Mobile, HDR 2D aktiv

**Was:** Renderer bleibt Forward Plus, rendering/viewport/hdr_2d = true.
**Warum:** Glow braucht HDR-Buffers. Ohne HDR 2D rendert Godot in LDR, Neon bleibt matt.
**Verworfen:** Compatibility (kein brauchbares Glow), Mobile (leichter, aber kein Vorteil hier).

## 2026-09-18 — Genre bleibt Arcade-Shooter, nicht Survivors-like

**Was:** Wellenbasierter Arcade-Shooter nach Roadmap M0-M11. Starforged Legacy ist reine Stilreferenz.
**Warum:** Ein Roguelite braucht Meta-Progression, Balancing ueber hunderte Runs und
Performance-Arbeit bei hunderten Gegnern — das ist nicht das erste Projekt.
**Verworfen:** Direkter Bullet-Heaven-Ansatz. Bleibt als moegliche v2 offen.
