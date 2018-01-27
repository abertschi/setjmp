# Exception Handling with setjmp.h in C

- https://en.m.wikipedia.org/wiki/Setjmp.h

## Usage
```c
    try_catch({
    // try block
    
    raise(42); // boom!

    }, {
    // catch block, exception_code (int) contains exception code (42 here)

    });
```

## Implementation
```c
/**
 * hand-knitted try/catch mechanism for c using setjmp mechanism.
 * Andrin Bertschi
 * 
 */
#include <stdio.h>
#include <setjmp.h>
#include <string.h>


static jmp_buf exception_ctx;
static int exception_code;


#define raise(errcode)                          \
  longjmp(exception_ctx, errcode);

#define try_catch(t, c)                                         \
  {                                                             \
    jmp_buf buf;                                                \
    memcpy(buf, exception_ctx, sizeof(buf));                    \
    if(exception_code = setjmp(exception_ctx), exception_code)  \
    {c}                                                         \
    else                                                        \
    {t}                                                         \
    memcpy(exception_ctx, buf, sizeof(buf));                    \
  }

int main(int argc, char *argv[argc])
{
  try_catch({
      printf("in try block 0\n");
      
      try_catch({
          printf("in try block 1\n");
          printf("raising exception in try block 1\n");
          raise(42);
      
          printf("i am never shown\n");          
        },{
          printf("in catch block 1\n");
          printf("exception is: %d\n", exception_code);          
        });

      printf("after try_catch block 1 \n");
      
      printf("raising exception in try block 0\n");
      raise(43);
      printf("i am never shown\n");          
    },{
      printf("in catch block 0\n");
      printf("exception is: %d\n", exception_code);
    });
  printf("after try_catch block 0 \n");

  return 0;
}

```
