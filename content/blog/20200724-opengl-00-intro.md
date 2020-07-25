+++
title = "OpenGL para as Massas, pt 0 -- Introdução"
date = 2020-07-24
description = "Uma introdução a uma série de tutoriais de OpenGL."

[taxonomies]
categories = ["Blog"]
tags = ["programming", "tutorial", "cg", "opengl"]
+++

**Nota**: esse artigo está sob constante atualização!

# Introdução

Um dos meus maiores interesses é computação gráfica. Na minha opinião, é uma
área muito subestimada. A maioria das pessoas com quem eu interajo no meu
dia-a-dia não têm interesse suficiente pra tirar um tempo e estudar, ou
simplesmente nem conhecem o que a área tem a oferecer. 

Talvez essa percepção minha seja resultado de um caráter específico do meu
espaço amostral. Na UFABC, a disciplina _Computação Gráfica_ é alocada perto
do final do curso seguindo os quadrimestres ideais do Bacharelado em Ciência
da Computação. E realmente, a galera tende a deixar essa disciplina
maravilhosa só pro final.

Aqueles que se aventuram e a cursam antes acabam se sentindo desapontados,
porque terminam a disciplina sem entender nada. Já vi muitas pessoas
reclamando que o curso é só um _copy-paste_ de slides, e muitos passam
dificuldades ao fazer o projeto final.

Além disso, essa disciplina será ofertada no próximo quadrimestre (que será
feito apenas online por causa do _micróbio desgraçado_), o que resultará numa
outra onda de alunos perdidos. E não ter o professor presencialmente pode
piorar a situação.

Visto tudo isso, tentando aumentar a moral da área (pelo menos pelos círculos
da UFABC), e defendendo os professores que lecionam essa disciplina (que são
professores muito bons), resolvi aproveitar que gastei minha adolescência
desenhando cubinhos com C++ e OpenGL--ao invés de aprender a paquerar--e
escrever um material que talvez possa ajudar alguém.

## Mas o que é OpenGL?

Segundo a [Wikipedia](https://en.wikipedia.org/wiki/OpenGL):

> _OpenGL (Open Graphics Library) is a cross-language, cross-platform_
> _application programming interface (API) for rendering 2D and 3D vector_
> _graphics. The API is typically used to interact with a graphics processing_
> _unit (GPU), to achieve hardware-accelerated rendering._

Ou seja, OpenGL é uma API usada para renderização eficiente de gráficos
vetoriais em 2D e 3D. Geralmente, é usada para interagir com placas de vídeo
(GPUs) para acelerar o processo de renderização.

Quando você joga Minecraft, por exemplo, o OpenGL está sendo utilizado por
baixo dos panos para enviar os dados dos polígonos a serem desenhados para a
GPU, além de coordenar o processo todo de renderização.

## Tá. O que é _Renderização_?

Essencialmente, renderização é o _processo de transformar uma descrição de uma
cena bi ou tridimensional em uma imagem que seria correspondente a uma foto
daquela cena_. O grau de realismo é importante e motiva diversas técnicas
diferentes, dependendo do que é almejado.

Um exemplo é a renderização fotorrealística _(que é a minha área)_. Aqui que
entram os famosos _raytracers_, usados por grandes estúdios em filmes e
animações (e ultimamente em jogos também. Viva RTX). Os algoritmos facilmente
viram extremamente complicados, envolvendo cálculo numérico e teoria de
probabilidade. 

Em OpenGL, podemos escolher quais técnicas utilizar, então nós mesmos vamos
escolher o grau de realismo (isso fará mais sentido ao longo da série). Vamos
focar em renderização em tempo real, que é o principal foco do OpenGL (e da
maioria das outras APIs gráficas, como Vulkan e DirectX).

Como renderização fotorrealística é complicada e leva tempo para ser
executada, vamos fazer uma _aproximação_. Ao invés de ray tracing, usamos a
técnica de _Rasterização_, que consiste em testar pixel por pixel, linha a
linha, e verificar se existe algum polígono que interceptaria tal pixel. Esse
processo é feito de maneira muito otimizada pela placa de vídeo, e nós apenas
nos preocuparemos em, basicamente, informar os dados necessarios para tal.

## Legal. Mas, por que OpenGL?

OpenGL é livre e multiplataforma, além de ser _fácil_. OpenGL, assim como
DirectX até a versão 11, é uma API de _alto nível_, então por baixo dos panos
o driver de vídeo está fazendo bastante trabalho por nós. Vulkan e DirectX 12
são de baixo nível -- são necessários muitos mais passos para fazer a mesma
coisa. Isso gera uma melhor oportunidade de otimização, sendo essa uma das
razões que as grandes engines de games de hoje em dia usam essas APIs.

Aqui, usaremos OpenGL _moderno_, que é como são conhecidas as versões da API a
partir da 3. Ele roda nativamente no desktop, mas existem versões alternativas
para diversos ambientes:

- OpenGL ES: voltado para sistemas embarcados, como smartphones e tablets;
- WebGL: voltado para ser executado em browsers.

Existem diferenças entre essas versões, mas são pequenas o suficiente para
você se sentir confortável e aprender uma outra versão rapidamente, dado que
já conhece bem uma.

## Gostei. Como será a série?

Assumirei que quem lê esta série é proficiente em alguma linguagem de
programação imperativa. Embora eu use Rust para meus projetos de computação
gráfica hoje em dia, todos os exemplos aqui serão escritos em C. Afinal,
provavelmente boa parte das pessoas que lerão nem conhecem Rust (se você não
conhece, [LEIA O LIVRO](https://doc.rust-lang.org/book/)) :(

Eu tentarei escrever um artigo por semana. Por enquanto, a ementa é a
seguinte:

- [Parte 0: Introdução](@/blog/20200724-opengl-00-intro.md)
- Parte 1: Setup e Criando uma Janela (e brincando com ela)
- Parte 2: A Pipeline e Buffers
- Parte 3: Shaders e seu Primeiro Triângulo
- Parte 4: Matrizes e Transformações, e seu Primeiro Cubo
- Parte 5: Texturas
- Parte 6: Framebuffers e Offscreen Rendering
- Parte 7: Algumas técnicas legais

Todo esse conteúdo está sujeito a alterações, enquanto eu planejo melhor o
minicurso. Espero que gostem!

Até a próxima parte!
