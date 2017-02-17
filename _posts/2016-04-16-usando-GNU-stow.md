---
layout:     post
title:      "GNU Stow"
subtitle:   "Organizando dotfiles."
date:       2016-04-16 18:02:00
author:     "Quatroka"
header-img: "https://i.imgur.com/fVn04gt.jpg"
comments : true
permalink: "5"
---

Recentemente me estressei bastante com meu Ubuntu ( que tecnicamente foi culpa
 minha pois o deixei uma bagunça) e resolvi experimentar outra distro: Arch Linux.
 Gostei muito dela, não só pelo fato dela vir somente com o essencial para o
 sistema funcionar e assim eu poder estudar enquanto "monto" o sistema de acordo
 com as minhas preferências e também por ser Rolling Release. 

E uma das coisas que decidi ao escolher ele foi tentar organizar também os "dotfiles".
 Primeiramente pensei em fazer algum script shell( que aliás eu já começei ),
 mas em meio de pesquisas sobre o novo _desktop environment_ que escolhi para
 o Arch (i3wm), achei um pacote chamado _GNU Stow_.

GNU Stow é um gerenciador de links do sistema para dotfiles. Ele cria link de
 sistema para _dotfiles_ de forma que você possa salvar todo os seus dotfiles
 em um único lugar, e toda vez que um software for utilizar um _dotfile_, ao
 invés de encontrar um arquivo, encontrá um link que levará ao arquivo original.
 Dessa forma além de reunir conseguir reunir todos os _dotfiles_ em um único
 lugar, também poderá criar arquivos versões diferentes de um mesmo arquivo e
 realizar a troca deles de forma mais pratica, apenas mudando a redirecionamento
 do _symlink_.

#### Usando Stow

Primeiramente vamos olhar os _dotfiles_ que possuímos e decidir quais iremos
 fazer symlinks, so caso usaremos como exemplo os pertencentes ao Vim e ao Zsh:

![](https://i.imgur.com/CFBmA3W.jpg)

Segundo, criaremos uma pasta chamada 'dotfiles' e nossa pasta de usuário mesmo.
 Dentro dela moveremos os arquivos do Vim e do Zsh, cada conjunto em um diretório.

![](https://i.imgur.com/8OvCBsR.jpg)

O GNU Stow trata cada diretório como um _pacote_, então para que ele possa gerar os
 symlinks, utilizaremos os nomes dos diretórios desta forma:

```
stow vim
stow zsh
```
> Caso queira mudar o lugar ou nome da pasta dotfiles(deault), crie a pasta pretendida,
>  e acrescente as opções `--dir=caminho/para/o/diretório` e `--target=caminho/onde/será/criado/o/symlink`.

![](https://i.imgur.com/f9ekl6O.jpg)

Pronto! Ele criou os symslinks ( o próprio terminal já identifica como um ), e
 você tem seus dotfiles reunidos em um só lugar. Outra vantagem do GNU Stow é a
 possibilidade de organizar não somente arquivos, mas ele também manter a estrutura
 que cada _pacote_ continha, desta forma por exemplo, podemos criar dois diretórios
 com os mesmos arquivos porém com _configurações_ diferente e assim mudar nosso
 ambiente de forma rápida e prática.

Para retirar os symlinks basta acrescentar a opção `-D` desta forma:

```
stow -D nomeDoPacote
```

> Não se esqueça que uma vez realizado o _unstow_ ele não moverá os arquivos para
 ao seu lugar original.


:)

---
Links:

>[GNU Stow](https://www.gnu.org/software/stow/)
