+++
title = "Uma Suave Introdução ao GDB"
date = 2020-03-16
description = "Um simples tutorial de como usar o GNU Debugger para salvar sua pele."

[taxonomies]
categories = ["Blog"]
tags = ["programming", "tutorial"]
+++

Lá no final de 2018, eu tive meu primeiro contato formal com o curso do
Bacharelado em Ciências da Computação na UFABC. Meu segundo quadrimestre na
universidade, não sabia absolutamente nada da vida, e eu estava lá, cursando
Programação Estruturada. Para quem não é da UFABC, é o curso que introduz
programação na gloriosa Linguagem C.

Como eu já sabia C há tempos, eu usei a disciplina pra polir meu conhecimento.
Uma coisa que eu também já sabia era como usar o GDB, o famoso _GNU Debugger_,
e de fato usei ele durante toda disciplina, me salvando em todos os
`segmentation fault (core dumped)`'s da vida.

Segui com meu caminho, e fiz Algoritmos e Estruturas de Dados I. Listas
ligadas, ordenação, árvore, yadda yadda yadda. Dança de ponteiros, `segfault`
aqui e ali, e eu com meu GDB.

Mas, hoje, são meus amigos que estão fazendo PE e AEDI. Eles também estão
passando pelos mesmos crashes que eu passei - mas eles nunca nem ouviram falar
do tal do GDB. Minha missão, então, é ajudá-los! 

# Masoq?

Da [página da Wikipedia](https://pt.wikipedia.org/wiki/Depurador), um depurador
(em inglês: debugger) é um programa de computador usado para testar outros
programas e fazer sua depuração, que consiste em encontrar os defeitos do
programa. 

Um debugger destrincha a execução de um programa. Você pode controlar e
observar ela da maneira que você quiser. 

# Como usar?

Para abrir um programa pelo GDB é bem simples. No seu terminal:

```sh
$ gdb executavel
```

Ele vai te cuspir com um monte de texto. Dê uma olhada:

```sh
GNU gdb (GDB) 9.1
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-pc-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from executavel...
(No debugging symbols found in executavel)
(gdb)
```

Depois do texto, ele te dá um shellzinho, ali no `(gdb) `. Aqui que a magia
acontece - você digita os comandos e o gdb executa. Vamos rodar o programa com
o comando `run`:

```
(gdb) run
Starting program: /tmp/gdb-tut/executavel
Ola mundo!
[Inferior 1 (process 150753) exited normally]
```

Beleza! Nosso programa executou e retornou normalmente, sem problemas. Nesse
caso, o GDB foi inútil. Estamos interessados em quando o programa explode.
Vamos, então ilustrar. Saia do GDB com o comando `quit` e tome, por exemplo,
esse código e salve ele como `segfault.c`:

```c
#include <stdio.h>

int *retorna_invalido() {
    int i = 420;

    return &i;
}

void explode() {
    int *ptr = retorna_invalido();

    printf("%d\n", *ptr);
}

int main() {
    int foo = 666;

    explode();

    return 0;
}

```

Claramente, o programa vai explodir. Estamos criando uma variável dentro da
função `retorna_invalido` e retornando um ponteiro para ela. Quando sairmos do
escopo da função, `i` vai ser dealocada e `&i` vai apontar para lixo. Vamos ver
o crash:

```
$ ./segfault
[1]    152031 segmentation fault (core dumped)  ./segfault
```

Ótimo. GDB to the rescue! Vamos abrir o `segfault` com o GDB:

```
$ gdb segfault
GNU gdb (GDB) 9.1
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-pc-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from segfault...
(No debugging symbols found in segfault)
(gdb) 
```

E vamos usar nosso comando `run` mais uma vez:
```
(gdb) run
Starting program: /tmp/gdb-tut/segfault

Program received signal SIGSEGV, Segmentation fault.
0x000055555555519c in main ()
```

Ótimo! O GDB já detectou que o segfault aconteceu na função `main`. Vamos usar
o comando `backtrace` para visualizar a pilha de chamadas até o momento da
explosão.

```
(gdb) backtrace
#0  0x000055555555519c in main ()
```

Hmmm ok. A violação de acesso ocorreu na instrução que estava no endereço
0x000055555555519c. Agora já sabemos onde procurar no nosso código! O quê? Você
não sabe qual a linha de código correspondente ao endereço 0x000055555555519c?
Como assim?!?! É mais que trivial!

# Compilando com informações de debug

Realmente, é super trivial. Mas, com um passo que não fizemos: compilar com as
informações de debug. Se você ler a mensagem que o GDB nos deu logo que abrimos
ele, você vai notar um "(No debugging symbols found in segfault)". Essas
informações são cruciais para termos uma experiência de debug no mínimo humana.

Quando compilamos um programa com informações de debug, o compilador anota no
executável informações como o nome do arquivo correspondente àquela
instrução, o número da linha, o nome das variáveis, o escopo e assim vai. O GDB
usa essas informações para nos dar diagnósticos úteis.

Para dizer ao compilador compilar com essas informações, precisamos apenas
passar uma flag na hora da compilação. No caso do `gcc` e do `clang`, basta
colocar a flag `-g` na linha de compilação. Por exemplo, se antes compilávamos
o arquivo `segfault.c` com `$ gcc segfault.c -o segfault`, agora compilamos com
`$ gcc segfault.c -g -o segfault`.

Depois disso, abra novamente o gdb: 

```
$ gdb segfault
GNU gdb (GDB) 9.1
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-pc-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from segfault...
(gdb)
```

A mensagem que diz que não existem símbolos não aparece mais! Vamos tentar
executar novamente:

```
(gdb) run
Starting program: /tmp/gdb-tut/segfault

Program received signal SIGSEGV, Segmentation fault.
0x000055555555519c in explode () at segfault.c:12
12	    printf("%d\n", *ptr);
```

# Investigando a Stack

Que coisa linda. Agora, temos o nome do arquivo, o número da linha e até a
função que causou a explosão. Nesse exemplo, já está bem claro o que aconteceu.
Mas, suponha que você esteja trabalhando num programa que usa árvores AVL e
o erro não foi esclarecido de primeira. Você pode procurar na pilha de chamadas
para verificar o caminho que o seu programa fez até chegar no crash. Para isso,
usamos o comando `backtrace` que vimos ali em cima:

```
(gdb) backtrace
#0  0x000055555555519c in explode () at segfault.c:12
#1  0x00005555555551c2 in main () at segfault.c:18
```

Ele mostrou que nossa pilha de chamadas tinha dois `frames`: um na função
`main`, que chamou a função `explode` que por sua vez criou outro.

Podemos investigar exatamente o que estava occorendo em cada frame no momento
do crash. Para isso, usamos o comando `frame N`, onde `N` é o número do frame
que o `backtrace` reportou. Por exemplo, vamos inspecionar o frame `1`:

```
(gdb) frame 1
#1  0x00005555555551c2 in main () at segfault.c:18
18	    explode();
```

Isso não é nada que não sabemos. O poder, aqui, é poder inspecionar variáveis
específicas. Note que dentro da `main`, criamos um `int foo`. Suponha que ele
seja importante para nós, e queremos ver qual o valor ele recebeu no momento do
crash. Para isso, usamos o comando `p var`, onde `var` é o nome da variável:

```
(gdb) p foo
$1 = 666
```

Que é exatamente o valor que colocamos no nosso código fonte.

# Conclusão

Isso é o mínimo que uma pessoa precisa saber para se salvar utilizando o GDB.
Sabendo isso, já é possível diagnosticar e arrumar vários `segfaults`, o que
vai poupar (e muito) seu tempo. No entanto, isso não é o suficiente para te
ajudar em problemas de lógica, quando o programa não crasha, mas não faz o que
você esperava que fizesse.

De fato, o conteúdo aqui não é nem 1% do que o GDB pode fazer. Numa parte 2
desse tutorial, vou ensinar a utilizar a função de execução linha-a-linha, onde
o seu código é executado passo a passo, e é possível inspecionar variáveis a
qualquer momento da execução.

Por enquanto, isso é tudo! Espero ter ajudado! :)
