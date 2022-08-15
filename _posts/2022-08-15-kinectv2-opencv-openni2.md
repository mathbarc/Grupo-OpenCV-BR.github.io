---
title:  "Utilizando Kinect v2 no Linux através da OpenCV"
author:
  name: Matheus Barcelos de Oliveira
  link: https://github.com/mathbarc
date:   2022-08-15 10:00:00 -0300
categories: [kinect, openni]
tags: [kinect, libfreenect, c++, linux, opencv, pcl, openni]
pin: false
---

# O que é o Kinect?

O Kinect é um sensor de movimentos desenvolvido para o Xbox 360 e Xbox One pela Microsoft em conjunto com a empresa Prime Sense. O Dispositivo possúi microfones, cameras RGB, projetores infravermelhos e um sensor CMOS monocromático. Utilizando os projectos e o sensor CMOS é possível estimar mapas de profundidade o que permite recriar a profundidade da cena. 

O dispositivo foi criado com o intuito permitir uma interface natural para que os usuários interagissem com jogos sem a necessidade de controles. Ele é capaz de detectar a pose de até 4 pessoas simultâneamente.

Neste artigo demonstrarei os passos necessários para utilizar o Kinect v2 no linux e como acessá-lo utilizando a opencv.

# Materiais

- [Kinect v2](https://produto.mercadolivre.com.br/MLB-2031065410-sensor-kinect-microsoft-xbox-one-original-novo-vitrine-nf-_JM#position=3&search_layout=grid&type=item&tracking_id=30704690-2e9a-4958-a95e-fb457fdb946a)
- [Adaptador Kinect v2 para PC](https://produto.mercadolivre.com.br/MLB-1895436597-adaptador-kinect-30-conector-xbox-one-x-one-s-windows-10-_JM)
- PC com linux

# Dependências

Durante a escrita deste tutorial, dois sistemas operacionais foram utilizados: Ubuntu 20.04 e Ubuntu 22.04, portanto aqui nesta sessão serão apresentados alguns comandos do gerenciador de pacotes APT que permitirão instalar os pacotes précompilados disponíveis. Para os usuários de outras distribuições estará disponível o link do repositório do github que pode ser usado para compilar as bibliotecas necessárias.

## OpenNI

A OpenNI (Open Natural Interaction) é um projeto Open Source com o objetivo de aprimorar a interoperabilidade de interfaces naturais de usuário, fornecendo um middleware que facilita acesso a dispositivos de interação natural para diferentes aplicações. 

A PrimeSense encerrou o projeto original OpenNI quando foi comprada pela Apple em novembro de 2013, porém seus antigos parceiros matém um fork da OpenNI 2 como um projeto open source.

- Repositório: [https://github.com/structureio/OpenNI2](https://github.com/structureio/OpenNI2)
- Comando APT: ```apt install libopenni2-dev openni2-utils```

## OpenCV

A OpenCV (Open Computer Vision) é uma biblioteca Open Source que reúne diversos algoritmos de visão computacional. A OpenCV foi construída para prover uma infraestrutura comum para aplicações de visão computacional possuindo suporte para diversas linguagens como: C++, python, java e MATLAB.

 - Repositório: [https://github.com/opencv/opencv](https://github.com/opencv/opencv)
 - Comando APT: ```apt install libopencv-dev```

Para realizar a instalação é recomendado os seguintes passos para garantia que se tenha uma versão da OpenCV com suporte a OpenNI2:

```bash
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
cd opencv_contrib
git checkout 4.6.0
cd ../opencv
git checkout 4.6.0
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=OFF -DBUILD_PERF_TESTS=OFF -DOPENCV_ENABLE_NONFREE=OFF -DOPENCV_EXTRA_MODULES=../opencv_contrib/modules -DWITH_OPENNI2=ON ..
make -j 4
sudo make install
```

## PCL

A PCL (Point Clout Library) é uma biblioteca escrita em C++ especializada no processamento de núvens de pontos. Esta biblioteca possuí algoritmos para filtro, seleção de pontos chave, reconstrução de superfícies, comunicação com sensores, visualização de núvens de pontos e outros tópicos.

 - Repositório: [https://github.com/PointCloudLibrary/pcl](https://github.com/PointCloudLibrary/pcl)
 - Comando APT: ```apt install libpcl-dev```

# Fazendo funcionar

As bibliotecas descritas na sessão anterior serão nossas interfaces de programação em um nível mais elevado, para que possamos extrair os dados do sensor primeiro é necessário um driver que gerenciará a comunicação entre o sistema operacional e kinect. Como os drivers oficiais do kinect são proprietários e apenas disponíveis na Kinect SDK para o windows, a comunidade inicio a criação de um drive para o linux: o freenect, desenvolvido para a primeira versão do kinect, e freenect2, desenvolvido para a segunda versão do kinect.

Neste artigo concentraremos no freenect2 pois estamos trabalhando com o Kinect v2. Nas seguintes sessões compilaremos o projeto [LibFreenect2](https://github.com/OpenKinect/libfreenect2) e configuraremos seu uso.

## Compilando a libfreenect2

```bash
git clone https://github.com/OpenKinect/libfreenect2.git
cd libfreenect2
git checkout v0.2.1
mkdir build
cd build
cmake -DBUILD_OPENNI2_DRIVER=ON -DCMAKE_BUILD_TYPE=Release ..
make -j 4
sudo make install
sudo make install-openni2
```

## Liberando acesso ao dispositivo para o usuário

Após compilar o projeto e instalar os artefatos, é necessário liberar o acesso ao dispositivo para os usuários, do contrário os programas que tentarem se comunicar com o kinect precisarão ser executados em modo root.

```bash
cd libfreenect2
sudo cp ./platform/linux/udev/90-kinect2.rules /etc/udev/rules.d/
```

**Após a execução dos comandos acima é necessário desconectar o kinect da porta USB e conectar novamente.**

## Testando com NiViewer2

Como primeiro teste rápido é possivel utilizar agora o NiViewer2 para obter dados do kinect.

```bash
sudo apt install openni2-utils # caso não tenha sido instalado ainda
NiViewer2
```

Após a execução dos comandos acima são esperados uma tela conforme a image e a seguinte saída no terminal:

![Tela NiViewer]({{site.baseurl}}/assets/img/imagens/kinect_v2_com_opencv/niviewer2.png "Tela NiViewer")


```
[Info] [Freenect2Impl] enumerating devices...
[Info] [Freenect2Impl] 10 usb devices connected
[Info] [Freenect2Impl] found valid Kinect v2 @2:2 with serial 067886733447
[Info] [Freenect2Impl] found 1 devices
[Info] [Freenect2DeviceImpl] opening...
[Info] [Freenect2DeviceImpl] transfer pool sizes rgb: 20*16384 ir: 60*8*33792
[Info] [Freenect2DeviceImpl] opened
[Info] [Freenect2DeviceImpl] starting...
[Info] [Freenect2DeviceImpl] submitting rgb transfers...
[Info] [Freenect2DeviceImpl] submitting depth transfers...
[Info] [Freenect2DeviceImpl] started
[Info] [DepthPacketStreamParser] 32 packets were lost
[Info] [DepthPacketStreamParser] 13 packets were lost
[Info] [TurboJpegRgbPacketProcessor] avg. time: 9.53263ms -> ~104.903Hz
```

# Programa de Teste

Feito o teste com o NiView2, a seguir está um programa em C++ utilizando a PCL e OpenCV para obter informação do sensor e exibir a núvem de pontos em uma tela interativa. Ao final está contido o vídeo com o resultado esperado ao se compilar o programa abaixo.

## Código Fonte


```cmake
find_package(OpenCV 4 COMPONENTS core highgui videoio imgcodecs)

if(${OpenCV_FOUND})
else()
        find_package(OpenCV 2 COMPONENTS core highgui REQUIRED)
endif()


find_package(PCL COMPONENTS visualization REQUIRED)

set(OPENNI_CAPTURE_INCLUDE_DIRS 
	${OpenCV_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
)

file(GLOB OPENNI_CAPTURE_SOURCES main.cpp)
include_directories(${OPENNI_CAPTURE_INCLUDE_DIRS})

add_executable(openni-capture ${OPENNI_CAPTURE_SOURCES})
target_link_libraries(openni-capture ${OpenCV_LIBS} ${PCL_LIBRARIES})
```

```c++
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <iostream>
#include <pcl/visualization/cloud_viewer.h>

#include <fstream>

cv::VideoCapture cap(1, cv::CAP_OPENNI2); // Configura o cv::VideoCapture para abrir o dispositivo 1 utilizando a OpenNI2

void getPointCloud(pcl::visualization::PCLVisualizer& viewer)
{
    viewer.removeAllPointClouds(); // Remove as núvens de pontos anteriores

    cv::Mat pointCloudFrame, frame;

    cap.grab(); // Obtém-se uma leitura do sensor
    cap.retrieve(pointCloudFrame, cv::CAP_OPENNI_POINT_CLOUD_MAP); // Obtendo núvem de pontos em formato matricial
    cap.retrieve(frame, cv::CAP_OPENNI_BGR_IMAGE); // Obtendo informação de cor alinhada com a núvem de pontos

    pcl::visualization::CloudViewer::ColorCloud* cloud = new pcl::visualization::CloudViewer::ColorCloud();

    for(int i = 0; i<pointCloudFrame.rows; i++) // Para cada ponto contido na matriz
    {
        for(int j = 0; j<pointCloudFrame.cols; j++) // Para cada ponto contido na matriz
        {
            cv::Vec3b color = frame.at<cv::Vec3b>(i,j); // Obtém a informação de cor
            pcl::PointXYZRGB point(color[2], color[1], color[0]); // Cria um ponto no formato da PCL passando a informação da cor contida nesse ponto

            cv::Vec3f coords = pointCloudFrame.at<cv::Vec3f>(i,j); // Obtém as coordenadas deste ponto
            point.x = coords[0]; // Adiciona a coordenada ao ponto
            point.y = coords[1]; // Adiciona a coordenada ao ponto
            point.z = coords[2]; // Adiciona a coordenada ao ponto

            cloud->push_back(point); // Adiciona o ponto na núvem
        }
    }

    viewer.addPointCloud(pcl::visualization::CloudViewer::ColorCloud::ConstPtr(cloud)); // Envia a núvem de pontos para ser renderizada pelo visualizador
}


int main(int argc, char** argv)
{

    if(!cap.isOpened())
       return -1;

    cap.set(cv::CAP_OPENNI_DEPTH_GENERATOR_REGISTRATION, 1); // Configurando OpenNI para gerar núvem de pontos com informações de cores


    pcl::visualization::CloudViewer viewer("point cloud"); // Inicializa um visualizador de núvens de pontos
    viewer.runOnVisualizationThread(getPointCloud); // Define a função utilizada para renderizar a cena

    while(!viewer.wasStopped()); // Aguarda a conclusão do visualizador de núvens de pontos

    cap.release(); // Encerra a captura do dispositivo
    
    return 0;

}
```

## Resultado
<div style="position:relative;width:100%;padding-bottom:56.25%">
<iframe style="position:absolute;top:0;left:0;width:100%;height:100%;border:0" src="https://www.youtube.com/embed/199c-im-FOM" allowfullscreen></iframe>
</div>

<div>
<h3>
<p>Caso tenha dúvidas me envie um <a href="mailto:matheusbarcelosoliveira@gmail.com">email</a>,</p>
<p><a href="https://github.com/mathbarc">Matheus Barcelos de Oliveira</a><br/>
Engenheiro de Visão Computacional</p>
</h3>
</div>
