---
layout: post
author:
  name: Andre Emidio
  link: [Linkedin](https://www.linkedin.com/in/andre-emidio/)
title:  "VCPKG como usar"
date:   2022-03-05 21:18:34 -0300
categories: c++ programação vcpkg compilar cmake opencv
---



Antes de tudo, o que é VCPKG ?


Te respondo , é um gerenciador de pacotes tal como Pip (Python) ou Maven (Java) para o C++, que carecia de um gerenciador tão poderoso como esse.

Criado pela Microsoft, visa centralizar, gerenciar e utilizar os pacotes com Cmake e seu compilador para ser de fácil utilização seus pelos desenvolvedores

Te levarei por cada passo que é importante fazer para que o VCPKG funcione de forma coerente.

Aahh ia me esquecendo, também funciona para o Linux.


Começando pelo começo.

Primeiro instale o Cmake em sua máquina.

Link :  [https://cmake.org/download/](https://cmake.org/download/)

<div class="image-container" style="display: flex; justify-content: center;">
    <img src="{{site.baseurl}}/assets/img/imagens/vcpkg/cmake_site.png"/>
</div>

Agora instale o Visual Studio Community, ele é necessário por conter o compilador de C e C++ necessários para compilar as bibliotecas.

Nota :  Usaremos a versão comunity em inglês nesse tutorial.

Ao instalar o Visual Studio, selecione Desenvolvimento para desktop em C++.

<div class="image-container" style="display: flex; justify-content: center;">
    <img src="{{site.baseurl}}/assets/img/imagens/vcpkg/ingles_visual_studio.png"/>
</div>


Usando o Git Bash para baixar Vcpkg atráves do PowerShell (administrador) iremos começar o procedimento de instalação.

```
git clone https://github.com/Microsoft/vcpkg.git
```

Acesse a pasta

```
cd vcpkg/
```

Digite os comandos abaixo:

```
.\bootstrap-vcpkg.bat

.\vcpkg.exe integrate install
```

Ao final da instalação, irá aparecer o seguinte resultado.

```
-DCMAKE_TOOLCHAIN_FILE=D:/andreemidio/libraries/vcpkg/scripts/buildsystems/vcpkg.cmake
```

Essa informação é importante para quando criar um novo projeto C++ e precisar linkar as bibliotecas, é nesse local que o Cmake irá montar as informações.

Mais abaixo irei mostrar como usar essa configuração numa IDE.

Adicionando o Auto complete:

```
.\vcpkg.exe integrate powershell
```

Ainda no terminal na pasta do clone do VCPKG, testa o funcionamento instale a lib Curl, que serve para fazer chamadas HTTP.

```
.\vcpkg.exe install sdl2 curl --debug
```


Usando o toolchain (VCPKG) em uma IDE

Para Clion da Jetbrains

<div class="image-container" style="display: flex; justify-content: center;">
    <img src="{{site.baseurl}}/assets/img/imagens/vcpkg/clion.gif"/>
</div>


Para o VSCODE da Microsoft 

```json
{
    "cmake.configureSettings": {
        "CMAKE_TOOLCHAIN_FILE": "D:/andreemidio/libraries/vcpkg/scripts/buildsystems/vcpkg.cmake" //Esse caminho se refere ao local onde instalei o VCPKG
    },
    "cmake.configureOnOpen": true,
}
```

<div class="image-container" style="display: flex; justify-content: center;">
    <img src="{{site.baseurl}}/assets/img/imagens/vcpkg/clion.gif"/>
</div>



[Andre Emidio](https://www.linkedin.com/in/andre-emidio/)

Desenvolvedor em Visão Computacional/Backend e membro do Grupo OpenCV Brasil.