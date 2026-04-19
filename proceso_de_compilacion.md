**P1.** Ejecutá `wc -l programa.i` y escribí el número de líneas que obtenés.

<!-- Completá la línea siguiente con el número exacto (solo dígitos, sin espacios): -->
LINEAS_I=821

¿Por qué ese número es tan mayor que las 94 líneas de `programa.c`?

> **R:** por la expansión de los headers estándar
 
---

> **P2.** Ejecutá `grep "Archivo fuente principal" programa.i`.
¿El comando encuentra algo o no devuelve nada?

<!-- Completá con SI (si encontró algo) o NO (si no encontró nada): -->
COMENTARIOS_EN_I=NO

¿Por qué ocurre eso?

> **R:** porque el preprocesador elimina los comentarios

---

**P3.** Ejecutá `grep -n "CUADRADO" programa.i` y copiá la salida completa.

> **R:** 
>```
>158:extern int __attribute__((__cdecl__)) __attribute__((__nothrow__)) __attribute__((__format__(__mingw_printf__,1,0))) __mingw_vprintf(const char*, __builtin_va_list);
>175: int __attribute__((__cdecl__)) __attribute__((__nothrow__)) __attribute__((__format__(__ms_printf__,1,0))) __msvcrt_vprintf(const char *, __builtin_va_list);
>781:    printf("=== Laboratorio de Compilacion en C (v%s) ===\n\n", "1.0");
>790:    printf("CUADRADO(%d)      = %d\n", 5, ((5) * (5)));
>```
¿El nombre `CUADRADO` aparece tal cual en `programa.i`, o fue reemplazado
por otra cosa? Respondé SI o NO:

<!-- Completá con SI o NO: -->
CUADRADO_EN_I=NO

---

**P4.** Ejecutá `grep -n '"1\.0"' programa.i` y copiá la línea encontrada.

> **R:** 781:    printf("=== Laboratorio de Compilacion en C (v%s) ===\n\n", "1.0");

¿Cuál era el nombre de la macro en `programa.c` que fue reemplazada por `"1.0"`?

<!-- Completá con el nombre exacto de la macro (en mayúsculas, como está en el fuente): -->
NOMBRE_MACRO_VERSION=VERSION

---

**P5.** Ejecutá los siguientes dos comandos y copiá la salida de cada uno:
```bash
gcc -E programa.c | grep "Iniciando"
gcc -E -DDEBUG programa.c | grep "Iniciando"
```

> **R:**el primero no devuelve nada, el segundo devuelve printf("[DEBUG] %s\n", ("Iniciando main"));

¿Agregar `-DDEBUG` hace que aparezca código nuevo en el `.i` que antes no estaba?
Respondé SI o NO:

<!-- Completá con SI o NO: -->
DEBUG_ACTIVA_CODIGO=NO

---

**P6.** En `programa.i` vas a encontrar líneas con este formato intercaladas entre el código C:

```
# 1 "<built-in>"
# 1 "/usr/include/stdio.h" 1 3 4
# 412 "/usr/include/stdio.h" 3 4
# 1 "programa.c"
```

Son **marcadores de línea** que el preprocesador inserta para registrar el origen de cada bloque. El formato es `# <número_de_línea> "<archivo>"`. El compilador los usa para reportar errores con el nombre y línea del archivo original (no del `.i`), y el debugger los usa para mapear instrucciones de máquina al fuente C.

Ejecutá este comando para ver de dónde viene la declaración de `printf`:

```bash
grep -n "stdio.h" programa.i | head -5
```

¿Qué información comunican esas líneas `# N "archivo"`? ¿De qué archivo proviene el bloque que contiene la declaración de `printf`?

> **R:** las lineas con # N "archivo" muestran la linea de origen (la linea del .c) de un bloque de codigo. Printf proviene del archivo stdio.h

---

**P7.** Ejecutá `grep "area_circulo" programa.s` y copiá la salida.

> **R:**
>```
>.ascii "area_circulo(%.1f) = %.4f\12\0"
>call    _printf
>call    _printf
>call    _printf
>call    _printf
>call    _area_circulo
>call    _printf
>call    _factorial
>call    _printf
>call    _printf
>.def    _printf;        .scl    2;      .type   32;     .endef
>.def    _area_circulo;  .scl    2;      .type   32;     .endef
>.def    _factorial;     .scl    2;      .type   32;     .endef
>```

¿`area_circulo` aparece como una función *definida* en `programa.s`
(con su propio bloque de instrucciones) o solo como una *llamada* (instrucción sin cuerpo)?
Respondé DEFINIDA o LLAMADA:

<!-- Completá con DEFINIDA o LLAMADA: -->
AREA_EN_S=LLAMADA

---

**P8.** Encontrá en `programa.s` la etiqueta `sumar:` o `_sumar:` y copiá
las primeras 4 líneas de instrucciones que le siguen.

> **R:**
>```
>_sumar:
>pushl	%ebp
>movl	%esp, %ebp
>movl	_llamadas, %eax
>addl	$1, %eax
>```

Explicá en términos generales qué hacen esas instrucciones
(usá los comentarios del laboratorio como guía):

> **R:**
>```
> pushl %ebp                  guarda el valor del base pointer en el tope del frame actual (_sumar)
> movl	%esp, %ebp          apunta el base pointer a la misma dirección que el stack pointer
> movl	_llamadas, %eax     lee el valor de la variable _llamadas y lo guarda en el eax
> addl	$1, %eax suma       uno al valor guardado en el eax
> ```

---

**P9.** Ejecutá `grep "llamadas" programa.s` y copiá la salida.

> **R:** 
> ```
>         .globl  _llamadas
>_llamadas:
>        movl    _llamadas, %eax
>        movl    %eax, _llamadas
>        movl    _llamadas, %eax
> ```
¿Aparece la variable `llamadas` en el ensamblador?
Respondé SI o NO:

<!-- Completá con SI o NO: -->
LLAMADAS_EN_S=SI

---

**P10.** Ejecutá `nm programa.o` y copiá la salida completa.

> **R:**
> ```
>00000000 b .bss
>00000000 d .data
>00000000 r .eh_frame
>00000000 r .rdata
>00000000 r .rdata$zzz
>00000000 t .text
>         U ___main
>         U _area_circulo
>         U _factorial
>00000132 T _imprimir_separador
>00000000 B _llamadas
>0000001a T _main
>         U _printf
>         U _puts
>00000000 T _sumar
>```
¿Con qué letra aparece `area_circulo` en esa tabla?
Escribí solo la letra (una mayúscula):

<!-- Completá con la letra exacta que muestra nm (U, T, D, etc.): -->
TIPO_AREA_EN_O=U

---

**P11.** ¿Por qué `area_circulo` tiene ese tipo en `programa.o`
pero tipo `T` en `matematica.o`?

> **R:** porque en programa.o area_circulo todavia no se encuentra definido, lo va a definir matematica.o

¿Qué etapa del proceso de compilación resuelve esa diferencia?
Respondé con una palabra: PREPROCESAMIENTO, COMPILACION, ENSAMBLADO o ENLAZADO:

<!-- Completá con una de las cuatro opciones: -->
ETAPA_QUE_RESUELVE=ENLAZADO

---

**P12.** Intentá ejecutar `./programa.o` directamente. ¿Qué mensaje aparece?

> **R:** bash: ./programa.o: cannot execute binary file: Exec format error

¿Se puede ejecutar un archivo `.o` directamente?
Respondé SI o NO:

<!-- Completá con SI o NO: -->
EJECUTABLE_O=NO

---

**P13.** Enlazá con `gcc programa.o matematica.o -o programa`.
Ejecutá `nm programa | grep "area_circulo"` y copiá la salida.

> **R:**
> ```
> 004015a8 T _area_circulo
> ```
¿Con qué letra aparece ahora `area_circulo` en el ejecutable final?
Escribí solo la letra:

<!-- Completá con la letra exacta que muestra nm: -->
TIPO_AREA_ENLAZADO=T

---

**P14.** Ejecutá `nm programa | grep "^ *U"` y copiá la salida.

> **R:**

¿Quedan símbolos de tipo `U` en el ejecutable final?
Respondé SI o NO:

<!-- Completá con SI o NO: -->
SIMBOLOS_U_FINAL=SI

¿Por qué quedan? ¿Quién los resuelve y cuándo?

> **R:**
> ```
>  U ___deregister_frame_info registra informacion de stack frames para manejo de excepciones
>  U ___register_frame_info   hace lo inverso al simbolo de arriba
>  U __Jv_RegisterClasses     compilador de java de GCC
>
>  quedan UNDEFINED porque su codigo esta en una libreria externa y se resuelven en tiempo 
>  de ejecucion en caso de ser necesarias
> ```

---

**P15.** Ejecutá `./programa` y copiá la salida completa.

> **R:**
> ```
> === Laboratorio de Compilacion en C (v1.0) ===
> 
> sumar(3, 4)       = 7
> CUADRADO(5)      = 25
> MAX(7, 12)        = 12
> ----------------------------------------
> area_circulo(5.0) = 78.5398
> Factoriales:
>   0! = 1
>   1! = 1
>   2! = 2
>   3! = 6
>   4! = 24
>   5! = 120
> ----------------------------------------
> Llamadas a sumar(): 1
> ```

¿Qué valor da `factorial(5)`? Escribí solo el número:

<!-- Completá con el número exacto: -->
FACTORIAL_5=120

---

**P16.** Explicá con tus palabras la diferencia entre una **macro función**
como `CUADRADO(x)` y una **función real** como `sumar(a, b)`.
¿En qué etapa "desaparece" cada una? ¿Cuál tiene verificación de tipos?

> **R:** la macro es una instruccino para el preprocesador que desaparece durante esta misma etapa y pasa a ser texto plano. En cambio una funcion real como sumar nunca desaparece en si, sino que se convierte en instrucciones de maquina. Asi tambien, sumar es la que tiene verificacion de tipos puesto que tiene una firma que los define.

---

**P17.** ¿Qué diferencia hay entre un símbolo de tipo `T` y uno de tipo `D`
en la salida de `nm`? ¿En qué sección del archivo objeto vive cada uno?

> **R:** un simbolo T es de tipo texto y es codigo ejecutable, en cambio un simbolo D es una variable global que tiene un valor inicializado en memoria.

---
