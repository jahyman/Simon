.data

array:
    .space 1024,0
arraySize = . - array // It should be 1024
size:
    .space 4,0        // 4 bytes == 1 word

.text

.global _start
_start:
    //r2 has a pointer to the first argument
    
    //r2 is zero if no argument is passed
    cmp     r2, #0
    beq	    workdone

    // get string size to r10
    and     r10, r10, #0  
    mov	    r3, r2        
onemore:
    ldrb    r5, [r3]
    add	    r10, r10, #1  
    add	    r3, r3, #1 
    cmp     r5, #0
    bne	    onemore       

    // copy string in reverse order
    sub     r3, r3, #2    // do not copy the zero, and decrease overcounted
    sub     r10, r10, #1  // decrease overcounted
    ldr     r4, =array
    and     r11, r11, #0
reverse:
    ldrb    r5, [r3]
    strb    r5, [r4]
    sub	    r3, r3, #1
    add     r4, r4, #1
    add     r11, r11, #1
    cmp	    r10, r11
    bge reverse

    // Now time to print array
    mov     r0, #1        // stdout
    ldr     r1, =array
    mov     r2, r10
    mov     r7, #4        // write is syscall #4
    swi     #0            // invoke syscall

workdone:
    /* syscall exit(int status) */
    mov     %r0, #0       /* status -> 0 */
    mov     %r7, #1       /* exit is syscall #1 */
    swi     #0            /* invoke syscall */
