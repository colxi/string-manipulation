# string-manipulation (FASM)

**16BIT x86 minimal library written in FASM assembly**, focused into provide the essential resources, for **String Manipulation**. Code is no OS dependant, so should run under all kind of 16BIT enviroments, and could be easilly ported to 32BIT architectures.

-----
###  Available procedures:

  - `strcmp` : Compare two strings.
  - `strlen` : Returns the length of a string
  - `strchr` : Search char in a string
  - `trim`   : Trims a string (modifies original)
  - `ltrim`  : Trims Left Spaces a string (modifies original)
  - `rtrim`  : Trims Right Spaces a string (modifies original)

###  Usage Specifications:
Procedure | Input Registers | Output
------------ | ------------- | -------------
`strcmp`   | Expects String1 pointer in the **AX** register, and String2 pointer in **BX** | **CX=1** if equal, **CX=0** if not equal
`strlen`  |  Operates with the string  pointer of the **AX** register | Characters count in **CX**
`strchr`  |  Expects the string pointer in **AX** and the char to find in *BL* | Pointer to char in **CX** (or NULL)
`trim` |   Operates with the string pointer of the **AX** register   |  Pointer to the trimed string in **AX**
`rtrim` |  Operates with the string  pointer of the **AX** register   |  Pointer to the trimed string in **AX**
`ltrim` |  Operates with the string  pointer of the **AX** register   |  Pointer to the trimed string in **AX**

**Input**: All provided Strings must be ended with a NULL character.

**Output trim, rtrim, ltrim**: All the *trim* procedures, modify the original string, and will remove the following characters from left, right or both sides of the string, depending the invokd procedura :

    - SPACEBAR chars          (" ")
    - TABS chars              (\t)
    - New Line chars          (\r)
    - Carraige return char    (\n)
    - Vertical TAB char       (\x0B)

:information_source: **`IMPORTANT: This library uses the STACK to preserve the values of the registers on procedure calls. Ensure you have setted your stack segment and pointer.`**

#### Usage Example:
The following code implements an enviroment to run the example conversion, and provides a print procedure (using BIOS interrupt) to output the result to screen.

```asm
use16

main:
    ; set Data and Stack Segments, and Stack Pointer
	xor	ax,	ax
	mov	ds,	ax
	mov	ax,	0x8000
	mov	ss,	ax
	mov	sp,	0x0000
    ;*** Trim String... ***
    mov ax, myString
    call    trim
    ; *** Conversion done ***
    call print
    halt:
    	hlt
    	jmp halt


print:
    mov si, ax              ; copy AX pointer to SI
    .nextChar:
        lodsb               ; load string byte and stores it in AL
        cmp al, 0
        je .done            ; If char is zero, end of string detected!
        mov ah, 0x0E        ; single character BIOS print service
        mov bh, 0x00        ; Page num 0
        mov bl, 0x07        ; Text Color attributes
        int 0x10            ; Call BIOS video interrupt
        jmp .nextChar       ; follow with next char
    .done:
        RET

myString db "    This is    a  test   ", 0

include 'lib/data-type-convert.asm'
```

### Todo

 - Implement more procedures ( toupper | tolower | strtoupper | strtolower )

