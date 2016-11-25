---
layout:     post
title:      "Sublime e Terminal"
subtitle:   "Embelezando as coisas."
date:       2016-01-13 00:17:00
author:     "Quatroka"
header-img: "https://i.imgur.com/koMcnli.jpg"
comments : true
permalink: "2"
---

Estou ansiosamente esperando o Ubuntu 16.04 LTS(sim, só porque tá bonito), e
 também estou precisando formatar o HD, então resolvi já deixar preparado
 alguns arquivos de configuração do Sublime Text 3 e Docker(este último em um
 post separado) e um bash para o Ubuntu o zsh.


Zsh é um _Bash_ que deixa as coisas mais fáceis em relação ao terminal. Para
 começar, instalaremos ele.

    sudo apt-get install zsh

Agora temos que definir o zsh como nosso bash principal.

    chsh -s /bin/zsh


Logo depois vamos fazer a coisas ficarem bonitas, vamos instalar um framework
para o zsh: _Oh My Zsh_. Lembrando que você deve possuir o git já instalado.

    sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

Feito isso vamos dar reboot no sistema para que todas as alterações sejam feitas.
 Ficará parecido com isto:
 ![](http://i.imgur.com/IVoQosi.png)


Agora é a vez do Sublime Text 3. O site oferece a opção de baixar o pacote _.deb_,
 porem para estrear o zsh instalaremos via linha de comando. Here we go.



    sudo add-apt-repository ppa:webupd8team/sublime-text-3
    sudo apt-get update
    sudo apt-get install sublime-text-installer

Para iniciar o Sublime utilizamos o comando _subl_. Pronto! Sublime em nossa
 disposição! O Sublime em si já faz bastante coisa, então por enquanto vamos 
 apenas deixar ele mais bonito e mais prático. Para instalar esses pacotes novos
 utilizaremos o _Package Control_, que nada mais é do que um gerenciador de
 pacotes para o Sublime. Aperte **ctrl+'**(aspas)(ou **View > Show Console**) e
 cole este código no console que irá abrir:

    import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)

Feito isso, reinicie o sublime. Aperte o **ctrl+shift+p** ele vai abrir uma especie
 de terminal para o sublime(um pouco parecido com Vim), para instalar os
 pacotes utilize _Package Control: Install Package_ ele irá abrir outro
 terminal idêntico ao anterior, porem com a lista de pacotes disponíveis para
 a instalação. Instalaremos os seguintes pacotes:

    * Git                     //Executar comando do git direto do Terminal.
    * Markdown Preview        //Um visualizador markdown
    * HTML5                   //Um pacotes que auxilia nos snippets do Sublime
    * Material Theme          //Tema no estilo Material Design
    * Material Theme - Appbar //Add-on do Tema.

Terminado de instalar os pacotes reinicie o Sublime para os pacotes serem ativados.
Hora de configurar tudo! Para isso vamos em **Preferences > Settings - User**.
Ele irá abrir um arquivo para configurações personalizadas do Sublime e de pacotes
 instalados. Cole esse código nele, altere os valores se achar necessário:

{% highlight javascript %}
{
    //Pacotes
    "ignored_packages":
    [
    	"HTML5",
    	"Vintage"
    ],



    //Ativação do Tema e colorscheme
    "theme": "Material-Theme.sublime-theme", // Ativa
    "color_scheme": "Packages/Material Theme/schemes/Material-Theme-Darker.tmTheme", // Define esquema de cores do editor

    //Opções do tema
    "material_theme_accent_lime"             : false,  // Define cor verde(lima) como destaque
    "material_theme_accent_orange"           : false,  // Define cor laranja como destaque
    "material_theme_accent_purple"           :  true,  // Define cor roxo como destaque
    "material_theme_accent_red"              : false,  // Define cor vermelho como destaque
    "material_theme_accent_yellow"           : false,  // Define cor amarelo como destaque
    "material_theme_bold_tab"                : false,  // Texto em Negrito nas guias
    "material_theme_compact_sidebar"         : false,  // Barra lateral compacta
    "material_theme_contrast_mode"           :  true,  // Define alto contraste no tema
    "material_theme_disable_fileicons"       : false,  // Ocultar ícones de tipo de arquivo
    "material_theme_disable_folder_animation": false,  // Desabilita animações das pastas
    "material_theme_disable_tree_indicator"  : false,  // Desabilita indicador de arquivo na barra lateral
    "material_theme_panel_separator"         :  true,  // Mostrar painel separador inferior
    "material_theme_small_statusbar"         : false,  // Define barra de status pequena
    "material_theme_small_tab"               : false,  // Define guias pequenas
    "material_theme_tabs_autowidth"          : false,  // Ativar largura automática das guias
    "material_theme_tabs_separator"          :  true,  // Mostrar separadores entre as guias

    //Extras do tema
    "overlay_scroll_bars"          : "enabled",  // Esconde barra de rolagem
    "line_padding_top"             :         3,  // Recuo interno na parte superior
    "line_padding_bottom"          :         3,  // Recuo interno na parte inferior
    "always_show_minimap_viewport" :      true,  // Sempre mostrar minimap
    "bold_folder_labels"           :     false,  // Negrito nos nomes das pastas
    "indent_guide_options":                      // Opções de indentação
    [
    	"draw_normal", 
    	"draw_active"  
    ],

    //Add-on do tema
    "material_theme_tree_headings" : true, // Deixa a parte superior com destaque maior
}
{% endhighlight %}

Calma estamos quase terminando, salve o arquivo. Agora vamos apenas colocar
 alguns atalhos no teclado para nos ajudar. Para isso **Preferences > Key Bindings - User**.

{% highlight javascript %}
[
    //ctrl+shift+m para ativar o Markdown Preview
    { "keys": ["ctrl+shift+m"], "command": "markdown_preview",
    "args": {"target": "browser", "parser":"markdown"} },
	
    //ctrl+f1 para reindentar o código
    { "keys": ["ctrl+f1"], "command": "reindent" },
]
{% endhighlight %}

![](http://i.imgur.com/i35Ny1Y.png)

Pronto! Terminamos de deixar tudo um pouco mais bonito, e os arquivo estão prontos,
 quando precisar das mesmas configurações é apenas substitui-los no lugar dos originais.

:)


---
Links:

>[Zsh](http://www.zsh.org/)
>
>[Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh)
>
>[Sublime Text 3](http://www.sublimetext.com/3)
>
>[Package Control](https://packagecontrol.io/)
>
>[Arquivos de configuração](https://github.com/leomonteiro/dotfiles/tree/master/Sublime)
