# Phase 1 Work Division — Mini Java Lexical Analyzer

**Course:** CSCI415 Compiler Design, Fall 2026  
**Phase:** 01 — Lexical Analyzer only  
**Deadline:** 30 October  
**Team size:** 4

Every member writes code. A discussion is held with the whole team, and every member must be able to explain the part they implemented. Do not use a lexer library or any ready-made function that performs lexical analysis.

---

## Shared contract (lock this first, about 1 hour, all four)

Agree on these before anyone codes a recognizer.

| Item | Agreement |
|---|---|
| Token | `lexeme`, `tokenType`, `line`, `column` |
| Output | `Lexeme: …, Token: …` one per line, same style as the sample |
| Errors | `Error: <message> '<lexeme>' at line N, column M.` The scanner continues after the bad token |
| Symbol table | Identifiers only. Fields: lexeme, token `IDENTIFIER`, first line and column |
| `true` / `false` | Token `BOOLEAN`, checked before identifiers |
| `String` | Keyword `STRING_TYPE`. Text inside quotes is `STRING` |
| `=` / `>=` / `+` | `ASSIGN` / `LOGOP` / `MATHOP`, matching the sample |
| `main` | The sample prints `MAIN`, and the sample is marked “similar, not exact”. The keyword list does not include `main`. Treat `main` as `IDENTIFIER` unless the keyword box in the PDF says otherwise |

### Token set

- **Keywords:** `CLASS`, `PUBLIC`, `STATIC`, `VOID`, `INT`, `FLOAT`, `BOOLEAN`, `CHAR`, `STRING_TYPE`, `IF`, `ELSE`, `WHILE`, `FOR`, `RETURN`
- **Names and literals:** `IDENTIFIER`, `NUMBER`, `STRING`, `BOOLEAN`
- **Operators:** `ASSIGN` (`=`), `MATHOP` (`+ - * / %`), `LOGOP` (`< > <= >= == !=`)
- **Delimiters:** `LPAR` `(`, `RPAR` `)`, `LCUR` `{`, `RCUR` `}`, `LSQU` `[`, `RSQU` `]`, `SEMICOLON` `;`, `COMMA` `,`, `DOT` `.`

Use longest match: `>=` before `>`, `==` before `=`.

The operator box in the PDF did not extract as text. Confirm the full operator list from that page before coding Person 3’s table.

---

## Person 1 — Scanner engine and driver

Owns the character cursor and the program entry point. This is the integration point.

- Track line and column while reading the source one character at a time.
- Skip whitespace.
- Call the other recognizers in the order below.
- Read the input file from the command line.
- Print the token stream, then the symbol table.

**Must be able to explain:** how the cursor moves, and how one token is chosen.

## Person 2 — Keywords, identifiers, symbol table

- Case-sensitive keyword map. `class` and `Class` are different lexemes.
- Identifier rule: starts with a letter or `_`, then letters, digits, and `_`.
- Valid: `age`, `studentName`, `total_marks`, `x1`, `_count`.
- A digit followed by letters or `_` (`1name`) is one invalid identifier, not a number plus a name.
- Insert every valid identifier into the symbol table.

**Must be able to explain:** why `Class` is not `class`, why `1name` is one error, and what the symbol table stores.

## Person 3 — Literals, operators, delimiters

- `NUMBER` for integers and floats: `10`, `0`, `25.5`.
- `STRING` for `'…'` and `"…"`, including the unclosed-string error.
- `BOOLEAN` for `true` and `false`.
- Operators and the nine delimiters listed above.
- A second dot in a number (`1.2.3`) is a lexical error.

**Must be able to explain:** longest match for `>=`, and why `'A'` is `STRING` while `String` is `STRING_TYPE`.

## Person 4 — Comments, errors, tests, report

Comments and the error reporter are implementation, so this person has code to defend.

- Skip `//` through the end of the line.
- Skip `/* … */`.
- Report an unclosed `/*` with the line and column where it started.
- Report unknown characters (example: `@` in `int x = 10 @ 5;`) with line and column, then continue.
- Write the input test file (sample program plus every error case).
- Assemble the 1–2 page report from notes written by the other three.

**Must be able to explain:** comment skipping, and the four required errors (bad character, bad identifier, unclosed string, unclosed comment).

---

## Order inside one token

Person 1 calls the recognizers in this order:

1. Skip whitespace (Person 1).
2. If the character is `/`, try a comment first (Person 4). If it is not a comment, it is a divide operator (Person 3).
3. A quote starts a string (Person 3).
4. A digit starts a number, or an invalid identifier when letters or `_` follow (Person 3, with Person 2’s invalid-identifier rule).
5. A letter or `_` is a keyword, `BOOLEAN`, or an identifier inserted into the symbol table (Person 2).
6. An operator or delimiter (Person 3).
7. Anything else is an invalid character (Person 4).

---

## Schedule

| When | Work |
|---|---|
| Today, first hour | Lock the contract. Add one shared `Token` type and empty function stubs |
| Today | Each person implements their recognizers and checks them on a few hand-written strings |
| 29 Oct | Person 1 wires the loop. Run the sample `Student` program and match the sample token stream |
| 29 Oct, evening | Run Person 4’s error file: `@`, `1name`, unclosed `"Ali`, unclosed `/*`, `class` vs `Class` |
| 30 Oct | Fix mismatches, print the symbol table, submit source, the input file, and the report |

---

## Deliverables

1. Source code.
2. An input text file of Mini Java test programs.
3. A 1–2 page report covering:
   - Tokens implemented.
   - How errors were handled.
   - Challenges faced and how they were solved.

---

## Sample the integration must match

Input:

```java
class Student {
    public static void main() {
        int age = 20;
        float grade = 85.5;
        String name = "Ali";
        if (grade >= 50) {
            grade = grade + 5;
        }
    }
}