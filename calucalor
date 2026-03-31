section .data
    prompt      db  "Enter statement : "
    prompt_len  equ $ - prompt
    newline     db  10

section .bss
    buf_in      resb  32
    char_buf    resb  1
    buf_result  resb  8

section .text
global _start

_start:
.loop:
    mov  rax, 1
    mov  rdi, 1
    lea  rsi, [rel prompt]
    mov  rdx, prompt_len
    syscall

    call read_line
    test rax, rax
    jle  .exit

    call parse_input
    call calculate
    call print_result

    jmp  .loop

.exit:
    mov  rax, 60
    xor  rdi, rdi
    syscall

read_line:
    push rbx
    push r12
    lea  r12, [rel buf_in]
    xor  rbx, rbx

.rl_loop:
    mov  rax, 0
    mov  rdi, 0
    lea  rsi, [rel char_buf]
    mov  rdx, 1
    syscall

    test rax, rax
    jle  .rl_eof

    movzx rax, byte [rel char_buf]
    cmp  rax, 10
    je   .rl_done

    mov  [r12 + rbx], al
    inc  rbx
    jmp  .rl_loop

.rl_done:
    mov  byte [r12 + rbx], 0
    mov  rax, rbx
    jmp  .rl_ret

.rl_eof:
    xor  rax, rax

.rl_ret:
    pop  r12
    pop  rbx
    ret

parse_input:
    push r12
    push r13
    lea  r12, [rel buf_in]

    call skip_space
    call read_uint
    mov  r13, rax

    call skip_space
    movzx rcx, byte [r12]
    inc   r12

    call skip_space
    call read_uint
    mov  rbx, rax
    mov  rax, r13

    pop  r13
    pop  r12
    ret

skip_space:
    movzx r8, byte [r12]
    cmp  r8, ' '
    je   .ss
    cmp  r8, 9
    je   .ss
    ret
.ss:
    inc  r12
    jmp  skip_space

read_uint:
    xor  rax, rax
.ru:
    movzx r8, byte [r12]
    cmp  r8, '0'
    jl   .ru_done
    cmp  r8, '9'
    jg   .ru_done
    imul rax, rax, 10
    sub  r8, '0'
    add  rax, r8
    inc  r12
    jmp  .ru
.ru_done:
    ret

calculate:
    cmp  rcx, '+'
    je   .add
    cmp  rcx, '-'
    je   .sub
    cmp  rcx, '*'
    je   .mul
    cmp  rcx, '/'
    je   .div
    xor  rax, rax
    ret

.add:
    add  rax, rbx
    ret

.sub:
    sub  rax, rbx
    ret

.mul:
    imul rax, rbx
    ret

.div:
    xor  rdx, rdx
    div  rbx
    ret

itoa:
    lea  rdi, [rel buf_result]
    xor  rcx, rcx

    test rax, rax
    jnz  .build
    mov  byte [rdi], '0'
    mov  rsi, rdi
    mov  rdx, 1
    ret

.build:
    test rax, rax
    jz   .reverse
    xor  rdx, rdx
    mov  r8, 10
    div  r8
    add  dl, '0'
    mov  [rdi + rcx], dl
    inc  rcx
    jmp  .build

.reverse:
    xor  r8, r8
    lea  r9, [rcx - 1]
.rev:
    cmp  r8, r9
    jge  .rev_done
    movzx r10, byte [rdi + r8]
    movzx r11, byte [rdi + r9]
    mov  [rdi + r8], r11b
    mov  [rdi + r9], r10b
    inc  r8
    dec  r9
    jmp  .rev
.rev_done:
    mov  rsi, rdi
    mov  rdx, rcx
    ret

print_result:
    call itoa
    mov  rax, 1
    mov  rdi, 1
    syscall

    mov  rax, 1
    mov  rdi, 1
    lea  rsi, [rel newline]
    mov  rdx, 1
    syscall
    ret
