# Two-Player Pong Game 🏓
### 8086 Assembly Language | MASM | Irvine32 | Visual Studio 2022

A fully working two-player Pong game written in pure 8086 Assembly Language.
No game engine. No high-level language underneath. Every pixel, every bounce,
every keypress handled through raw assembly instructions.

Built as a semester project for Computer Organization and Assembly Language (COAL)
— BS Computer Science Semester 4, Air University Islamabad.

---



---

## 🎮 Controls
| Player | Move Up | Move Down |
|--------|---------|-----------|
| Player 1 (Cyan — Left) | W | S |
| Player 2 (Yellow — Right) | I | K |
| Quit | ESC | — |

First to **5 points** wins.

---

## ⚙️ How It Works

The entire game runs on **8 memory variables**:

```asm
ballX     BYTE  39    ; ball column
ballY     BYTE  12    ; ball row
ballDX    SBYTE 1     ; horizontal speed (+1 right, -1 left)
ballDY    SBYTE 1     ; vertical speed   (+1 down,  -1 up)
paddle1Y  BYTE  10    ; player 1 paddle top row
paddle2Y  BYTE  10    ; player 2 paddle top row
score1    BYTE  0     ; player 1 score
score2    BYTE  0     ; player 2 score
```

Every frame the game loop does 6 things:
1. Read keyboard (non-blocking via Irvine32 `ReadKey`)
2. Move ball (`ADD ballDX to ballX`, `ADD ballDY to ballY`)
3. Check collision (wall bounce = `NEG ballDY`, paddle bounce = `NEG ballDX`)
4. Draw everything (`Gotoxy` + `WriteChar` for each object)
5. Check win condition (`CMP score, 5`)
6. Delay 40ms (`Delay` — controls frame rate)

---

## 🧠 Assembly Concepts Covered

- **Data Movement** — `MOV`, `MOVZX`, `MOVSX`, `LEA`
- **Arithmetic** — `ADD`, `INC`, `DEC`, `NEG`
- **Branching** — `CMP`, `JE`, `JNE`, `JL`, `JG`, `JA`, `JB`, `JZ`, `JNZ`, `JMP`
- **Loops** — `LOOP` instruction + manual `DEC/JNZ` loops
- **Procedures** — 19 `PROC/ENDP` blocks connected via `CALL/RET`
- **Stack** — implicit use through every `CALL` and `RET`
- **Memory** — `.data` segment with `BYTE`, `SBYTE`, `EQU` constants
- **I/O** — Irvine32 procedures wrapping INT 10h and INT 16h
- **Bit manipulation** — `SHL` and `OR` for color attributes

---

## 🐛 Bugs We Actually Hit

**Score not updating**
Score variables incremented in memory but screen showed 0. Root cause: `WriteDec`
and `WriteString` internally corrupt `EAX` and `EDX`. Fixed by replacing everything
in `DrawScore` with direct `WriteChar` calls only — full register control.

**Ball passing through paddles**
After vertical paddle check, `EAX` still had `ballY` in it. Edge scoring condition
was comparing `ballY` thinking it was `ballX` — so scoring never triggered.
Fixed by freshly reloading the required variable before each comparison.

**Wrong winner on ESC**
`ShowWinner` only checked `score1 == WIN_SCORE`. If ESC ended the game early,
neither score equalled 5 so it defaulted to Player 2 wins every time.
Fixed by comparing both scores directly with `CMP` — higher score wins.

**USES directive syntax error**
`USES eax, ebx` on procedures with no parameters threw syntax errors in MASM.
Fixed by removing all `USES` directives — registers used as scratch within
procedures, which is standard MASM practice.

---

## 🚀 How to Run

1. Open Visual Studio 2022
2. Set up a MASM project with Irvine32 linked
3. Add `pong.asm` to the project
4. Press `Ctrl+F5` to run without debugging

> Make sure Irvine32.inc and the Irvine32 library are correctly configured
> in your Visual Studio project. This is the standard setup used in COAL labs.

---

## 📚 References

- Kip R. Irvine — *Assembly Language for x86 Processors*, 7th Edition
- Irvine32 Library Documentation
- ASCII Character Table
- MASM Programmer's Guide — Microsoft

---

