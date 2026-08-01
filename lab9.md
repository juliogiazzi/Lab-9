# IPC144 — Lab 9

Julio Cesar Silvestre Giazzi — jcsilvestre-giazzi

## Problem 1: Debugging

- Line 1 -- `int countSame(char s1[], char s2[])` **(S)**
- Line 3 -- `rc = 0;` (rc used uninitialized) **(L)**
- Line 7 -- `for (i = 0; i < len1 && i < len2; i++){` (i must start at 0; join checks with &&) **(L)**
- Line 8 -- `s1[i] = tolower(s1[i]);` (must store the result) **(L)**
- Line 9 -- `s2[i] = tolower(s2[i]);` (must store the result) **(L)**

## Problem 2: Debugging

- Line 5 -- `int max` / `MAX` must be defined **(S)**
- Line 7 -- `for (i = 0; i < max - 1; i++){` **(L)**
- Line 9 -- remove the stray `i++;` inside the while loop **(L)**
- Line 13 -- `return count;` (must return the count, not 0) **(L)**
- Line 14/15 -- `int count = 0;` (declare + initialize; `total` is unused) **(L)**
- Line 24 -- `char str[CAPACITY];` **(S)**
- Line 26 -- `generateRandomString(str, CAPACITY);` **(L)**
- **(P)** -- `strlen(str)` is recomputed every iteration. Store it once before the loop (`int len = strlen(str);`) and compare against `len`.

## Problem 3a: Debugging

- Line 11 -- `int len1 = strlen(collection->s1);` (collection is a pointer, use ->) **(S)**
- Line 12 -- `int len2 = strlen(collection->s2);` **(S)**
- Line 14 -- `if(strcmp(collection->s1, collection->s2) < 0){` (-> fix **(S)**; >0 orders the later string first — flip to <0 **(L)**)
- Line 15-16 -- `strcpy(str, collection->s1); strcat(str, " "); strcat(str, collection->s2);` (copy into str, don't reassign the pointer; add the missing space) **(L)**
- Line 19-20 -- `strcpy(str, collection->s2); strcat(str, " "); strcat(str, collection->s1);` **(L)**
- Line 23 -- `else if(len1 < len2){` (extra closing parenthesis) **(S)**
- Line 24-25 -- `strcpy(str, collection->s1); strcat(str, " "); strcat(str, collection->s2);` **(L)**
- Line 27 -- `else {` (`len2 > len1` repeats the len1<len2 case; the len1>len2 case is never handled) **(L)**
- Line 28 -- `strcpy(str, collection->s2);` (missing semicolon + -> / copy fix) **(S)**

## Problem 3b: Debugging

- Line 7 -- `struct StringCollection collection1 = {"daisy", "lilac"};` (name is StringCollection, no trailing s) **(S)**
- Line 8 -- `struct StringCollection collection2 = {"turtle", "dove"};` **(S)**
- Line 9 -- `struct StringCollection collection3;` **(S)**
- Line 12 -- `combine(temp, &collection1);` (combine takes a pointer) **(S)**
- Line 13 -- `strcpy(collection3.s1, temp);` (collections3 -> collection3) **(S)**
- Line 15 -- `combine(collection3.s2, &collection2);` (colleciton3 -> collection3; add &) **(S)**
- Line 16 -- add `return 0;` before the closing brace **(S)**
- **Note:** with MAX = 10 the combined strings (e.g. "daisy lilac" = 11 chars) overflow the 10-byte buffers.

## Walkthrough

Initial: `s1="grjm"  s2="RM"  s3=" fQyP!"  final=""`

( `·` = a space; **bold** = what changed on that line )

Function calls are traced at their call line (climb @19/25, fall @22, go @38/41); `i` is the loop counter inside that function.

| Line # | s1 | s2 | s3 | final | i | O/P |
|---|---|---|---|---|---|---|
| 13-17 | grjm | RM | ·fQyP! | "" | ? | |
| 19 | **G**rjm | | | | 0 | |
| 19 | G**R**jm | | | | 1 | |
| 19 | GR**J**m | | | | 2 | |
| 19 | GRJ**M** | | | | 3 | |
| 19 | | | | | 4 | |
| 20 | | | | | | 1: GRJM |
| 22 | | **r**M | | | 0 | |
| 22 | | r**m** | | | 1 | |
| 22 | | | | | 2 | |
| 23 | | | | | | 2: rm |
| 25 | | | | | 0 | |
| 25 | | | ·**F**QyP! | | 1 | |
| 25 | | | | | 2 | |
| 25 | | | ·FQ**Y**P! | | 3 | |
| 25 | | | | | 4 | |
| 25 | | | | | 5 | |
| 25 | | | | | 6 | |
| 26 | | | | | | 3: ·FQYP! |
| 28 | | | | **GRJM** | | |
| 29 | | | | | | 4: GRJM |
| 31 | | | | GRJM**·** | | |
| 32 | | rm**!·** | | GRJM·**rm!·** | | |
| 33 | | | | | | 5: GRJM·rm!· |
| 35 | | | | GRJM·rm!·**GRJM** | | |
| 36 | | | | | | 6: GRJM·rm!·GRJM |
| 38 | | | | **J**RJM·rm!·GRJM | 0 | |
| 38 | | | | J**U**RM·rm!·GRJM | 1 | |
| 38 | | | | JU**M**M·rm!·GRJM | 2 | |
| 38 | | | | JUM**P**·rm!·GRJM | 3 | |
| 38 | | | | | 4 | |
| 38 | | | | JUMP·**u**m!·GRJM | 5 | |
| 38 | | | | JUMP·u**p**!·GRJM | 6 | |
| 38 | | | | | 7 | |
| 38 | | | | | 8 | |
| 38 | | | | JUMP·up!·**J**RJM | 9 | |
| 38 | | | | JUMP·up!·J**U**RM | 10 | |
| 38 | | | | JUMP·up!·JU**M**M | 11 | |
| 38 | | | | JUMP·up!·JUM**P** | 12 | |
| 38 | | | | | 13 | |
| 39 | | | | | | 7: JUMP·up!·JUMP |
| 41 | | | | | 0 | |
| 41 | | | ·**D**QYP! | | 1 | |
| 41 | | | ·D**O**YP! | | 2 | |
| 41 | | | ·DO**W**P! | | 3 | |
| 41 | | | ·DOW**N**! | | 4 | |
| 41 | | | | | 5 | |
| 41 | | | | | 6 | |
| 42 | | | | | | 8: ·DOWN! |
| 44 | | | | JUMP·up!·JUMP**·DOWN!** | | |
| 45 | | | | | | 7: JUMP·up!·JUMP·DOWN! |

`go(3, ...)` adds 3 to each letter; `go(-2, ...)` subtracts 2; spaces and `!` are non-alphabetic so they're skipped.

### Final output, top to bottom

```
1: GRJM
2: rm
3:  FQYP!
4: GRJM
5: GRJM rm! 
6: GRJM rm! GRJM
7: JUMP up! JUMP
8:  DOWN!
7: JUMP up! JUMP DOWN!
```

(The last line is labeled "7:" in the given program — a typo in the source; reproduce it as printed.)
