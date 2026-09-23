# Space Shooter mit Godot — Roadmap & Lernplan

**Projekt:** Top-down Arcade Space Shooter
**Repo:** https://github.com/dasHund1982/godot_spaceshooter
**Engine:** Godot 4.7.x (aktuell stabil: 4.7.2, 16.08.2026)
**Sprache:** GDScript
**Stand:** 18.09.2026

---

## 0. Zwei Dinge vorweg

**Repo-Name: erledigt.** ✅ `godot_spaceshooter` (vorher `gadot`). Wenn du dem Spiel irgendwann einen echten Titel gibst, kannst du jederzeit nochmal umbenennen — GitHub leitet alte URLs automatisch weiter.

**Version festnageln.** Godot-Minor-Releases (4.6 → 4.7) brechen gelegentlich Dinge. Entscheide dich jetzt für **4.7.2**, schreib sie ins README, und update erst zwischen Meilensteinen — nie mittendrin. Ein einzelner Entwickler hat nichts davon, jeder Version hinterherzulaufen.

---

## 1. Repo-Setup (einmalig, ~1 Stunde)

### 1.1 Ordnerstruktur

```
/
├─ .gitignore
├─ .gitattributes
├─ README.md
├─ LICENSE
├─ project.godot
├─ docs/
│   ├─ ROADMAP.md          ← dieses Dokument
│   ├─ DECISIONS.md        ← Architekturentscheidungen, kurz
│   └─ LEARNING.md         ← dein Lerntagebuch
├─ scenes/
│   ├─ player/             player.tscn + player.gd
│   ├─ enemies/
│   ├─ projectiles/
│   ├─ pickups/
│   ├─ ui/
│   └─ levels/
├─ autoload/               game_state.gd, event_bus.gd, audio.gd
├─ resources/              custom Resources: enemy_stats.gd, wave.gd
├─ assets/
│   ├─ sprites/
│   ├─ audio/
│   └─ fonts/
└─ addons/
```

**Konvention:** Script liegt neben seiner Szene, nicht in einem globalen `/scripts`-Ordner. Das ist in Godot üblich und erspart dir die ewige Suche „wo war nochmal das Script zu dieser Szene".

### 1.2 Namenskonventionen (offizieller Godot-Styleguide)

| Was | Stil | Beispiel |
|---|---|---|
| Dateien & Ordner | snake_case | `enemy_drone.tscn`, `enemy_drone.gd` |
| Node-Namen im Baum | PascalCase | `MuzzlePoint`, `HealthBar` |
| Klassen (`class_name`) | PascalCase | `class_name EnemyDrone` |
| Variablen & Funktionen | snake_case | `var move_speed`, `func take_damage()` |
| Konstanten | ALL_CAPS | `const MAX_HEALTH = 3` |
| Private Member | führender Unterstrich | `var _cooldown_left` |
| Signale | Vergangenheitsform | `signal health_changed`, `signal died` |

### 1.3 `.gitignore`

```gitignore
# Godot 4 Cache & Build-Artefakte
.godot/
/android/
/builds/
*.translation

# Export-Presets enthalten ggf. Pfade zu Keystores/Signaturen
export_presets.cfg

# OS / Editor
.DS_Store
Thumbs.db
.vscode/
```

> `.godot/` ist der Import-Cache und wird bei jedem Öffnen neu erzeugt. Wenn du ihn committest, hast du bei jedem Commit hunderte geänderte Dateien. Das ist der häufigste Anfängerfehler mit Godot + Git.

`.gitattributes` für saubere Diffs und Zeilenenden (du bist auf Windows):

```
* text=auto eol=lf
*.gd text eol=lf
*.tscn text eol=lf
*.tres text eol=lf
*.png binary
*.ogg binary
*.wav binary
```

### 1.4 Git-Workflow

Auch als Einzelentwickler: arbeite so, wie du im Team arbeiten würdest. Genau das ist der „professionelle" Teil deiner Frage.

- **`main` ist immer lauffähig.** Nie direkt auf `main` committen. In GitHub: Settings → Rules → Branch-Regel für `main`, „Require a pull request".
- **Ein Branch pro Feature:** `feat/player-movement`, `feat/enemy-spawner`, `fix/bullet-leak`.
- **Conventional Commits** — kurz, englisch, im Imperativ:
  ```
  feat(player): add 8-way movement with screen clamping
  fix(bullet): free projectiles when leaving viewport
  refactor(enemy): extract health into reusable component
  docs: add collision layer table
  chore: bump Godot to 4.7.2
  ```
- **Pull Request auf dich selbst.** Klingt albern, ist es nicht: du liest deinen eigenen Diff nochmal, schreibst eine Beschreibung, und hast später eine lesbare Historie. Mit „Squash and merge" bleibt `main` sauber.
- **Tags für Meilensteine:** `git tag -a v0.3.0 -m "Milestone 3: enemies and collision"` — damit kannst du jederzeit zurück zu einem spielbaren Stand.

### 1.5 Projektmanagement auf GitHub

- **Milestones** = M0 bis M11 aus Abschnitt 3.
- **Issues** = ein Issue pro Feature, dem Milestone zugeordnet. Schreib in jedes Issue eine **Definition of Done** (was muss ich sehen können, damit es fertig ist).
- **Projects (Board)**: Spalten `Backlog → Next → In Progress → Done`. Maximal **eine** Karte in „In Progress". Das ist die einzige Regel, die Hobbyprojekte vor dem Versanden rettet.
- Issue-Nummer in die Commit-Message (`feat(player): add shooting (#12)`) — GitHub verlinkt das automatisch.

### 1.6 README

Schreib es **jetzt**, nicht am Ende. Minimal:

```markdown
# Starfall
Top-down Arcade Space Shooter, gebaut mit Godot 4.7.2.
Ein Lernprojekt für GDScript.

## Setup
1. Godot 4.7.2 installieren (https://godotengine.org/download)
2. Repo klonen
3. In Godot "Import" → project.godot auswählen

## Steuerung
WASD / Pfeiltasten — bewegen · Leertaste — schießen

## Status
Aktueller Meilenstein: M2 — Schießen
```

Später: GIF vom Gameplay ganz oben. Das ist der beste ROI im ganzen README.

---

## 2. Bevor du losbaust: vier Projekteinstellungen

Die kosten zusammen 15 Minuten und ersparen dir später Tage.

**a) Auflösung & Skalierung** — Project Settings → Display → Window:
- Viewport Width/Height: `640 × 360` (oder `480 × 270`) für Pixel-Art, `1920 × 1080` für hochauflösende Assets
- Stretch Mode: `canvas_items`, Aspect: `keep`
- Damit läuft dein Spiel in jedem Fenster ohne Umbau.

**b) Input Map** — Project Settings → Input Map. Lege `move_up/down/left/right`, `shoot`, `pause` an und frage **nie** direkt Tasten ab. Sonst kostet dich Gamepad-Support später eine Woche.

**c) Kollisionslayer benennen** — Project Settings → Layer Names → 2D Physics. Plane sie einmal, halte dich dran:

| Layer | Name | Wer ist drauf | Kollidiert mit (Mask) |
|---|---|---|---|
| 1 | `player` | Spielerschiff | 3, 4, 5 |
| 2 | `player_bullet` | Spielergeschosse | 3 |
| 3 | `enemy` | Gegner | 1, 2 |
| 4 | `enemy_bullet` | Gegnergeschosse | 1 |
| 5 | `pickup` | Powerups | 1 |

Merksatz: **Layer = „was bin ich", Mask = „worauf reagiere ich".** 90 % aller „warum trifft das nicht"-Probleme in Godot sind vertauschte Layer und Masks.

**d) Renderer**: `Mobile` oder `Compatibility` reicht für einen 2D-Shooter völlig und startet schneller als `Forward+`.

---

## 3. Die Meilensteine

Jeder Meilenstein: ein Branch, ein PR, ein Tag, ein spielbarer Stand. Wenn du merkst, ein Meilenstein zieht sich über Wochen — er ist zu groß geschnitten, teile ihn.

Zeitangaben gehen von **4–6 Stunden pro Woche** aus. Sie sind Orientierung, kein Vertrag.

---

### M0 — Skelett (Woche 1)

**Ziel:** Ein leeres Projekt startet, das Repo ist sauber, ein blaues Rechteck steht auf schwarzem Grund.

- Repo umbenennen, klonen, Godot-Projekt anlegen
- `.gitignore`, `.gitattributes`, README, Ordnerstruktur
- Die vier Einstellungen aus Abschnitt 2
- `main.tscn` als Hauptszene, Placeholder-Sprite

**GDScript:** `_ready()`, `print()`, wie ein Script an einen Node kommt.
**Done, wenn:** Frisch geklont, `F5` gedrückt, Fenster geht auf. Kein `.godot/` im Repo.

---

### M1 — Der Spieler bewegt sich (Woche 1–2)

**Ziel:** Schiff fährt mit WASD, kann den Bildschirm nicht verlassen.

- `CharacterBody2D` (oder `Area2D`, für einen Shooter reicht das oft)
- `Input.get_vector("move_left", "move_right", "move_up", "move_down")`
- `@export var speed: float = 300.0` → im Inspector einstellbar, ohne Code anzufassen
- Clamping an die Viewport-Grenzen

**GDScript-Themen:** Typisierte Variablen (`var x: float`), `@export`, `_physics_process(delta)` vs. `_process(delta)`, Vector2, `delta`.

> **Gewöhn dir statische Typen sofort an.** `var speed: float = 300.0` statt `var speed = 300.0`. Godot wird schneller, der Editor gibt dir Autovervollständigung, und du findest Fehler beim Tippen statt beim Spielen. Das ist die eine Angewohnheit, die dich von „Tutorial-Code" wegbringt.

**Done, wenn:** Bewegung fühlt sich gut an, ist framerate-unabhängig, Schiff bleibt im Bild.

---

### M2 — Schießen (Woche 2–3)

**Ziel:** Leertaste feuert Geschosse, die nach oben fliegen und verschwinden.

- Eigene Szene `bullet.tscn` (Area2D + Sprite + CollisionShape2D)
- `const BULLET = preload("res://scenes/projectiles/bullet.tscn")`, dann `BULLET.instantiate()`
- Geschoss an einen `Marker2D` („MuzzlePoint") am Schiff hängen
- Feuerrate über `Timer` oder einen Cooldown-Zähler
- **`VisibleOnScreenNotifier2D` → `screen_exited` → `queue_free()`**

**GDScript-Themen:** Szenen instanziieren, `preload` vs. `load`, `add_child()`, `queue_free()`, erste Signalverbindung.

> **Die häufigste Falle im ganzen Projekt:** Geschosse, die den Bildschirm verlassen und nie gelöscht werden. Nach zwei Minuten hast du 5.000 Nodes und das Spiel ruckelt. Räum von Anfang an auf — und instanziier Geschosse **nicht** als Kind des Spielers, sonst fliegen sie mit ihm mit.

**Done, wenn:** 60 Sekunden dauerfeuern, und der Node-Zähler im Debugger bleibt stabil.

---

### M3 — Gegner & Treffer (Woche 3–4)

**Ziel:** Gegner fliegen herunter, Geschosse zerstören sie, Kollision mit dem Spieler tut weh.

- `enemy.tscn` mit Bewegung nach unten, Health, `area_entered`-Signal
- Kollisionslayer aus Abschnitt 2 konsequent verdrahten
- Spieler-Health + Zerstörung
- Gruppen: `add_to_group("enemies")` für spätere Massenabfragen

**GDScript-Themen:** Signale (`signal died`, `.connect()`, `.emit()`), Gruppen, `is_instance_valid()`.

**Done, wenn:** Man kann gewinnen und verlieren. Nicht schön, aber ein Spiel.

---

### M4 — Wellen & Spawner (Woche 4–5)

**Ziel:** Gegner kommen in definierten Wellen, nicht zufällig aus dem Nichts.

- Spawner-Node mit `Timer`
- **Wellen als eigene Resource** statt als Dictionary im Code:
  ```gdscript
  class_name Wave extends Resource
  @export var enemy_scene: PackedScene
  @export var count: int = 5
  @export var interval: float = 0.8
  @export var formation: String = "line"
  ```
  Dann legst du Wellen als `.tres`-Dateien im Inspector an und balancierst das Spiel, **ohne eine Zeile Code zu ändern**. Das ist der Godot-Weg und der wichtigste Aha-Moment in diesem Projekt.

**GDScript-Themen:** Arrays, Dictionaries, `for`-Schleifen, custom `Resource`, `PackedScene`, `RandomNumberGenerator`.

**Done, wenn:** Drei unterschiedliche Wellen laufen nacheinander, definiert in `.tres`-Dateien.

---

### M5 — HUD & Spielzustand (Woche 5–6)

**Ziel:** Score, Leben und Wellennummer stehen auf dem Bildschirm und stimmen.

- `CanvasLayer` für UI (bewegt sich nicht mit der Kamera)
- Autoload `GameState` (Score, Lives, Wave)
- Autoload `EventBus` mit Signalen wie `score_changed`, `player_died`

**GDScript-Themen:** Autoloads/Singletons, Signale als Entkopplung, Control-Nodes und Anchors.

> **Architektur-Faustregel:** Das UI fragt den Spieler nicht nach seinem Zustand. Der Spieler sendet ein Signal, das UI hört zu. Sobald du anfängst, mit `get_node("../../UI/ScoreLabel")` durch den Szenenbaum zu greifen, wird dein Projekt unwartbar. Die Regel dagegen heißt **„call down, signal up"**: nach unten Methoden aufrufen, nach oben Signale senden.

**Done, wenn:** Ein Gegnertreffer erhöht den Score — und weder Gegner noch Score-Label wissen voneinander.

---

### M6 — Spielfluss (Woche 6–7)

**Ziel:** Hauptmenü → Spiel → Game Over → Neustart. Pause-Funktion.

- `get_tree().change_scene_to_file()`
- `get_tree().paused` + `process_mode` auf dem Pausenmenü
- Game-Over-Screen mit Endscore

**GDScript-Themen:** Szenenwechsel, Pause-System, Button-Signale.

**Done, wenn:** Du kannst 10 Runden hintereinander spielen, ohne die Engine neu zu starten. → **Tag `v0.6.0`. Ab hier ist es ein vorzeigbares Spiel.**

---

### M7 — Juice (Woche 7–8)

Der Meilenstein mit dem besten Verhältnis von Aufwand zu gefühlter Qualität. Nichts hier ist neue Mechanik — alles ist Feedback.

- Hit-Flash (Sprite kurz weiß via `modulate` + `Tween`)
- Screen Shake bei Explosionen
- `GPUParticles2D` für Explosionen und Triebwerk
- Sound: Schuss, Treffer, Explosion (`AudioStreamPlayer`)
- Scrollender Sternenhintergrund (`ParallaxBackground` / `Sprite2D` mit Region)
- Kurzer Hit-Stop (`Engine.time_scale` für 0,05 s auf 0,1)

**GDScript-Themen:** `Tween`, `AnimationPlayer`, Audio-Bus, Partikel.

**Done, wenn:** Jemand, der zuschaut, sagt „oh, das fühlt sich gut an".

---

### M8 — Gegnertypen & Powerups (Woche 8–10)

**Ziel:** Vier bis fünf Gegnertypen mit unterschiedlichem Verhalten, drei Powerups.

- Basis-Szene `enemy_base.tscn`, spezialisierte Szenen erben davon (Godot: „Scene Inheritance")
- Verhalten: gerade / Sinuskurve / zielend / schießend / kamikaze
- Powerups: Doppelschuss, Schild, Speed

**GDScript-Themen:** `extends`, `class_name`, Vererbung vs. Komposition, `super()`.

> **Merk dir für später:** Wenn du das dritte Mal „dieser Gegner braucht auch Health" denkst, mach Health zu einer eigenen Komponente (ein `HealthComponent`-Node, den du überall hinhängst), statt sie in jede Klasse zu kopieren. Komposition schlägt tiefe Vererbungsbäume — das ist auch außerhalb von Gamedev der Kern von sauberem Design.

---

### M9 — Boss (Woche 10–11)

**Ziel:** Ein Endgegner mit drei Phasen und Healthbar.

- Zustandsautomat: `enum State { ENTER, PHASE_1, PHASE_2, PHASE_3, DYING }` und `match state:`
- Angriffsmuster pro Phase
- Boss-Healthbar im HUD

**GDScript-Themen:** `enum`, `match`, State Machines, Koroutinen (`await get_tree().create_timer(1.0).timeout`).

---

### M10 — Persistenz & Optionen (Woche 11–12)

- Highscore-Tabelle in `user://highscores.cfg` (`ConfigFile` — einfacher als JSON)
- Options-Menü: Lautstärke Master/SFX/Musik, Vollbild
- Einstellungen überleben den Neustart

**GDScript-Themen:** `FileAccess`, `ConfigFile`, `user://` vs. `res://`, `AudioServer`.

> `res://` ist im exportierten Spiel **schreibgeschützt**. Alles, was gespeichert werden soll, gehört nach `user://`. Ein Fehler, der im Editor nie auffällt und im Export sofort.

---

### M11 — Release (Woche 12–13)

- Export-Templates installieren, Windows-Build + Web-Build (HTML5)
- Icon, Fenstertitel, Versionsnummer
- README mit Gameplay-GIF und Download-Link
- **GitHub Release** mit Tag `v1.0.0` und angehängter ZIP
- Optional: itch.io-Seite — kostenlos und das übliche Zuhause für solche Projekte

**Done, wenn:** Jemand ohne Godot dein Spiel per Doppelklick starten kann.

---

## 4. Wenn du Lust auf den Profi-Teil hast

Nicht nötig für das Spiel, aber genau die Dinge, die du im Beruf brauchst:

- **GitHub Actions**: Build bei jedem Push auf `main` (Godot läuft headless, `--export-release`). Erster echter CI-Kontakt mit einem Projekt, das dir gehört.
- **GUT** (Godot Unit Test, im AssetLib): Unit-Tests für die Logik-Teile — Score-Berechnung, Wellengenerator. Für Gameplay selbst lohnen Tests kaum, für Regeln schon.
- **`gdlint` / `gdformat`** (Python-Paket `gdtoolkit`) als Pre-Commit-Hook: konsistenter Stil, ohne darüber nachzudenken.
- **`docs/DECISIONS.md`**: drei Zeilen pro Entscheidung — *Was, Warum, Alternative verworfen weil*. In sechs Monaten ist das Gold.

---

## 5. Lernressourcen

| Ressource | Wofür |
|---|---|
| [Offizielle Godot-Doku: „Your first 2D game"](https://docs.godotengine.org/en/stable/getting_started/first_2d_game/) | **Mach das zuerst.** 2–3 Stunden, deckt M0–M3 im Kleinen ab. |
| [GDScript-Referenz](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html) | Nachschlagewerk, kein Lesebuch |
| [GDScript Style Guide](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_styleguide.html) | Reihenfolge im Script, Namensgebung |
| [Best Practices](https://docs.godotengine.org/en/stable/tutorials/best_practices/) | Szenenorganisation, Autoloads, wann Signale |
| [Kenney.nl](https://kenney.nl/assets) | CC0-Grafik und Sound, Space-Shooter-Packs inklusive |
| [GDQuest](https://www.gdquest.com/) | Gute Videos zu Godot 4, wenn Text nicht dein Format ist |

**Lernstrategie:** Ein Tutorial ganz durcharbeiten (das offizielle), danach **keine Tutorials mehr** — nur noch Doku nachschlagen, wenn du vor einem konkreten Problem stehst. Tutorial-Hopping fühlt sich produktiv an und lehrt nichts. Der Moment, in dem du „ich brauche jetzt einen Timer" denkst und in der Doku nachschaust, ist der Moment, in dem du lernst.

Führ `docs/LEARNING.md`: pro Session drei Zeilen — was gebaut, was nicht verstanden, was nachgeschlagen. Nach zwei Monaten siehst du dort Fortschritt, den du im Code nicht siehst.

---

## 6. Die sechs Fallen

1. **Scope Creep.** „Multiplayer wäre cool", „Ein Upgrade-Baum wäre cool." Schreib es in ein Issue mit Label `post-1.0` und bau es nicht. Ein fertiges kleines Spiel ist mehr wert als drei unfertige große.
2. **Assets zu früh.** Bau alles mit farbigen Rechtecken bis M6. Grafik ersetzt man in einer Stunde, verlorene Wochen nicht.
3. **Vergessene `queue_free()`.** Siehe M2. Beobachte den Node-Zähler im Debugger.
4. **Layer/Mask vertauscht.** Siehe Abschnitt 2c.
5. **Alles in `_process()`.** Physik und Bewegung gehören in `_physics_process()` — das läuft mit festem Takt, `_process()` nicht.
6. **Große Binärdateien im Git.** Bleib unter 100 MB pro Datei. Wenn du irgendwann Musik in WAV einwirfst: vorher in OGG konvertieren, nicht Git LFS nachrüsten.

---

## 7. Dein nächster Schritt

1. ~~Repo umbenennen~~ ✅
2. Godot 4.7.2 installieren, offizielles „Your first 2D game"-Tutorial durcharbeiten
3. Danach: M0-Branch aufmachen, Struktur anlegen, PR, mergen, `v0.0.1` taggen

Erst dann M1.

---

*Erstellt am 18.09.2026*
