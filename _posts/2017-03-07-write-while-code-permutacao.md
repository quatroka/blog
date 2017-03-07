---
layout:     post
title:      "Write while code - Permutação"
subtitle:   "Organizando dotfiles."
date:       2017-03-02 17:47:02
author:     "Quatroka"
header-img: "https://i.imgur.com/koMcnli.jpg"
comments : true
permalink: "6"
---


> 02/03/2017 19:02

Recentemente me candidatei a uma vaga Estágio em Desenvolvimento
 na empresa **Pin People**, e umas das etapas é uma _task_ envolvendo
 perguntas e realização de alguns problemas.

E uma parte disso era resolver o **"195 - Anagram" do UVa Online Judge**.
 De cara veio uma alegria, pois envolve um conceito de
 estatística: **permutação**; e um pouco de desespero, pois
 converter uma expressão matemática em um algoritmo eficiente
 não é uma tarefa fácil.


### Pensando no problema

> 02/03/2017 20:13

Existem duas formas de resolver este problema:


1. A maneira prática e rápida, na qual utilizarei o módulo _itertools_ com o método _permutations_ para Python e o classe Array com o método _permutation_. 

2. A maneira lenta e provavelmente pouco eficiente, transpor uma expressão matemática para um algoritmo.

Apesar da primeira maneira ser muito tentadora, o novo aprendizado
 é quase NULO, logo a graça e a diversão de se programar
 resolvendo problemas assim também se perde.

Pois bem, vamos fazer os dois.

### Uma pausa para pensar em estatística

> 02/03/2017 20:45

Para chegarmos no tópico de permutação, antes temos que
 lembrar de algumas coisas: fatorial, princípio fundamental
 da contagem(ou princípio multiplicativo) e arranjo.

#### Fatorial

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/c6d958a1c8c4d5178acf4a7375cea3f5037884c8)

Fatorial é uma operação matemática que dado um número _n_,
 multiplica todos numeros naturais inteiros positivos
 menores que _n_.

 De maneira mais simples através de exemplos:

    # Para representar fatorial, utilizamos o símbolo _!_
    4! = 4 * 3 * 2 * 1 = 24
    3! = 3 * 2 * 1 = 6
    0! = 1 # Existe um motivo, porém não abordaremos(link no final).

#### Princípio fundamental da contagem

É literalmente contar quantas possibilidades possíveis entre
 os elementos pode-se realizar.

De maneira mais simples através de exemplos:

    # Fulano vai em um restaurante novo na cidade. Chegando lá, eles tem oferecem somente 1 opção de prato formada pelo seguintes items: 1 massa, 1 acompanhamento e uma salada.

    # Opções de massa: Arroz, Macarrão.
    # Opções de acompanhamento: carne, frango, peixe. 
    # Opções de salada: Pepino, tomate, alface.

    # Quantas opções de pratos fulano poderá pedir?

    Para responder isso basta usar o princípio fundamental da contagem e multiplicar a quantidade de elementos de cada categoria entre si.

        massa * acompanhamento * salada = 2 * 3 * 3 = 18 possibilidades de pratos.

#### Combinatória

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/44cab36c6323c2ab0fd481ab36d12a7f864a2b8f)

Permutação é como se fosse uma modificação do princípio fundamental da contagem porém a **ordem importa** e **não há repetições**.

De maneira mais simples através de exemplos:

    # Podemos simplificar aquela expressão matemática com Pn = n! para este caso(link #2 no final)
    # Usaremos o problema a ser resolvido como exemplo, e a string "ABC"

    3 letras, 3 posições e sem repetições -> n = 3

    Pn = n!
    P3 = 3!
    P = 6 # ABC, ACB, BAC, BCA, CAB, CBA;

Esta expressão matemática apenas nos retorna a quantidade
 de possibilidades possíveis e não a sequencia em si, como
 comentei acima. Porém esse resultado pode nos ser útil,
 através de uma forma de testar o código! _It"s a test!_

### Maneira #1

> 03/03/2017 02:47

Como quase não usei a linguagem Ruby, não tenho parâmetros
 de padrões utilizados por ela, vamos pesquisar! Tentarei
 utilizar um _Style guide_ que se mostrou bem completo no
 github e para teste utilizarei o módulo padrão do Ruby.
 Vamos lá:


tests/test_uva_195.rb
```ruby
# Test File for UVa problem 195 - Anagram
# https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=131

require_relative "../uva_195_with_modules"
require "test/unit"


# Test class for testing permutations.
class TestPermutation < Test::Unit::TestCase
  # Test if method get_amount_results returns the correct
  # permutation amount.
  def test_amount_results
    assert_equal(6, get_permutation_list("abc").length)
    assert_equal(24, get_permutation_list("abcd").length)
    assert_equal(1, get_permutation_list("aaa").length)
    assert_equal(3, get_permutation_list("aab").length)
  end
end
```

uva_195.rb
```ruby
# File to resolve UVa problem 195 - Anagram.
# https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=131


# Receive a letters sequence return your permutation.
#
# * Receive a letters sequence in string format, split that
# * in a list of characters and create a list of permutation
# * with that in a list of permutation in ascending order.
def get_permutation_list(letters_sequence)
  letters_sequence = letters_sequence.split(//)

  permutation_list = letters_sequence.permutation.to_a

  permutation_list = permutation_list.map{|x| x.join("")}

  return permutation_list.uniq.sort
end
```

Funcionou? Sim. Está correto? Não. O teste está falho! Se
 testarmos uma string que tenha caracteres repetidos, o
 teste falha. Vamos acrescentar mais um teste, finalizar
 o algoritmo e refatorar o teste, que é ~~grotescamente~~
 bem simples:

tests/test_uva_195.rb
```ruby
# Test File for UVa problem 195 - Anagram
# https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=131

require_relative "../uva_195_with_modules"
require "test/unit"


# Test class for testing permutations.
class TestPermutation < Test::Unit::TestCase
  # Test if method get_amount_results returns the correct
  # permutation amount.
  def test_amount_results
    assert_equal(6, get_permutation_list("abc").length)
    assert_equal(24, get_permutation_list("abcd").length)
    assert_equal(1, get_permutation_list("aaa").length)
    assert_equal(3, get_permutation_list("aab").length)
  end

  # Test if method get_permutation_list returns the 
  # permutation list corretily.
  def test_permutation_list
    assert_equal(["aaa"], get_permutation_list("aaa"))
    assert_equal(["abc", "acb", "bac", "bca", "cab", "cba"],
                  get_permutation_list("abc"))
  end
end
```

uva_195.rb
```ruby
# File to resolve UVa problem 195 - Anagram.
# https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=131


# Receive a letters sequence return your permutation.
#
# * Receive a letters sequence in string format, split that
# * in a list of characters and create a list of permutation
# * with that in a list of permutation in ascending order.
def get_permutation_list(letters_sequence)
  letters_sequence = letters_sequence.split(//)

  permutation_list = letters_sequence.permutation.to_a

  permutation_list = permutation_list.map{|x| x.join("")}

  return permutation_list.uniq.sort
end

n_cases = gets.to_i

result_list = []
for i in 1..n_cases do
  permutation_list = get_permutation_list(gets.chomp)
  result_list << permutation_list
end

for j in 0..(result_list.length - 1) do
    puts result_list[j]
end
```

### Maneira #2

> 05/03/2017 11:16

Depois de um tempo analisando meu código, e tentando
 resolver o problema sem usar módulos da própria linguagem,
 descobri algumas coisas interessantes:

* Para resolver o problema do Anagrama, para cada letra
  extra que é usada no teste na sequencia de caracteres, sua
  fatorial aumenta, logo, quando é usado uma sequencia de 10
  caracteres, 10! = 3.628.800 items! Isso execede a capacidade
  de sua lista(testada em Ruby e Python) que quando executados,
  capaz de encher 100% de sua RAM e SWAP! Logo para resolver
  este problema será necessário gerar um item da permutação
  por vez(algo similar a método _next_permutation()_ em C++)
  e imprimi-lo logo em seguida.

![](http://i.imgur.com/GaK7DkO.jpg)

 * Juntando esse problema das listas com fazer um algoritmo
   que não utiliza-se módulos, passei esses dois ultimos dias
   pensando em uma solução. Porém como o tempo é curto,
   pesquisei sobre algoritmos de permutação. O primeiro que
   encontrei foi algoritmo de Heap's(Heap's algorithm) na
   qual uma de suas formas, utiliza recursividade e muita
   _swap_ de index. Mas já está pronto, então a emoção de
   fazer a coisa, se perde completamente.

 * Pesquisando um pouco mais encontro um blog de OCaml que
   tem vários posts de algoritmos e suas explicações. Entre
   estes posts, um destes posts ele dava o exemplo do algoritmo
   de Johnson-Trotter(que mais tarde fui descobrir que ele
   era um dos mais eficientes) e claro: escrito em OCaml.
   E como no post ele explica de uma maneira fácil de entender,
   o desafio fica transferir o algoritmo de OCaml para Python
   e Ruby.


> 07/03/2017 03:13

Depois varias pesquisas e tentativas para fazer o algoritmo
 de Johnson-Trotter(em Python ou Ruby), percebi que não seria
 capaz de faze-lo. Outro fator que me desmotivou a tentar
 continuar foi ler isto em um blog:

    So keep that in mind if you run into Time Limit errors
    when submitting Java solutions. It may not be a problem
    with your algorithm.
 
 Obs.:o post foi escrito por Duncan Smith, que aparentemente
 já/é foi Software engineer da Microsoft, então pode-se ter
 uma maior confiança em seu post.

Por mais que eu tentasse enviar a submissão até em python,
 provavelmente iria dar Time Limit, ou seja, brigar em uma
 batalha já perdida. E juntando o fato de não conseguir 
 transferir o algoritmo de OCaml para Python e os motivos
 citados acima, chego a conclusão que para entrega dessa
 atividade a Pin People apenas usando a maneira #1. Porém
 foi uma semana de muito aprendizado.

 :)

---
Links:

>[Pin People](https://www.gnu.org/software/stow/)
>
>[UVA Problem 105](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=131)
>
>[Fatorial](https://pt.wikipedia.org/wiki/Fatorial)
>
>[Fatorial de zero](https://bauparalelo.wordpress.com/2011/02/18/por-que-0-1/)
>
>[Princípio fundamental da contagem](http://www.infoescola.com/matematica/principio-fundamental-da-contagem/)
>
>[Permutação #1](https://pt.wikipedia.org/wiki/Permuta%C3%A7%C3%A3o)
>
>[Permutação #2](http://www.infoescola.com/matematica/permutacao/)
>
>[Ruby doc - Array](https://ruby-doc.org/core-2.4.0/Array.html#method-i-permutation)
>
>[Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide)
>
>[Python doc - Itertools](https://docs.python.org/3/library/itertools.html#itertools.permutations)
>
>[Algoritmo enchendo RAM e SWAP](http://i.imgur.com/es0Yiua.png)
>
>[next_permutation() - C++](http://www.cplusplus.com/reference/algorithm/next_permutation/)
>
>[Heap's algorithm](https://en.wikipedia.org/wiki/Heap's_algorithm)
>
>[Permutations - OCaml](http://typeocaml.com/2015/05/05/permutation/)
>
>[Comparação de performance de algoritmos de permutação](https://arxiv.org/pdf/1205.2888.pdf)
>
>[How to attack a programming puzzle](https://www.redgreencode.com/how-to-attack-a-programming-puzzle/)
