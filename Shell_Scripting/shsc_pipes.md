# Pipes

## What are Pipes?

- Pipes (`|`) let you send the output of one command as input to another, and we can chain as much as we want.
- Syntax:  
    ```
    [command 1] | [command 2] | [command 3] ... | [command n]
    ```
- Pipes and filters help solve data processing problems by combining simple commands.

---

## Example: Combining Commands

### Using sort and uniq commands Together:

```
$ sort pets.txt | uniq
cat
dog
goldfish
parrot
```
- `sort` groups duplicates together, `uniq` removes them, leaving only unique lines.

---

## Applying Commands to Strings and Files

Some commands (like `tr`) only accept standard input, not filenames.

### Example: Replace vowels with underscores

```
$ echo "Linux and shell scripting are awesome!" | tr "aeiou" "_"
L_n_x _nd sh_ll scr_pt_ng _r_ _w_s_m_!
```

### Example: Replace consonants with underscores

```
$ echo "Linux and shell scripting are awesome!" | tr -c "aeiou" "_"
_i_u__a_____e______i__i___a_e_a_e_o_e_
```

### Example: Convert file contents to uppercase

```
$ cat pets.txt | tr "[a-z]" "[A-Z]"
GOLDFISH
DOG
CAT
PARROT
DOG
GOLDFISH
GOLDFISH
```

### Combine with `uniq` for unique uppercase lines

```
$ sort pets.txt | uniq | tr "[a-z]" "[A-Z]"
CAT
DOG
GOLDFISH
PARROT
```

---

## Extracting Information from JSON Files

Suppose you have a JSON file `Bitcoinprice.txt`:

```json
{
    "coin": {
        "id": "bitcoin",
        "name": "Bitcoin",
        "symbol": "BTC",
        "price": 57907.78008618953,
        ...
    }
}
```

### Extract the "price" field using `grep`:

```
$ cat Bitcoinprice.txt | grep -oE "\"price\"\s*:\s*[0-9]*\.?[0-9]*"
```

- `-o`: Only print the matching part.
- `-E`: Use extended regex.
- `\"price\"`: Match the string "price".
- `\s*`: Match any whitespace.
- `:`: Match the colon.
- `[0-9]*\.?[0-9]*`: Match the number (with optional decimal).

---

