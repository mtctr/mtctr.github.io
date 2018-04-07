
<!--
You can use HTML elements in Markdown, such as the comment element, and they won't be affected by a markdown parser. However, if you create an HTML element in your markdown file, you cannot use markdown syntax within that element's contents.
-->

## 2. Manipulação de pixels - Exercícios
### Exercício 1  

Este programa, a partir de dois pontos P1(x,y) e P2(x,y) escolhidos pelo usuário, inverte as cores dos pixels localizados na região entre esses pontos.

#### Código:
```cpp
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace cv;
using namespace std;

int main (int, char**){
  Mat image;
  Vec3b val;
  int p1x, p1y, p2x, p2y;

  image =  imread("img/saladca.png", CV_LOAD_IMAGE_COLOR);

  if(!image.data){
    cout << "imagem não encontrada" << endl;
  } else{
    resize(image, image, Size(960,540));
  }

  namedWindow("janela",WINDOW_AUTOSIZE);

  cout<<"Escolha um ponto P1(x) entre 0 e "<<image.cols<<":" << endl;
  cin>>p1x;
  cout<<"Escolha um ponto P2(x) entre 0 e "<<image.cols<<":" << endl;
  cin>>p2x;

  cout<<"Escolha um ponto P1(y) entre 0 e "<<image.rows<<":" << endl;
  cin>>p1y;
  cout<<"Escolha um ponto P2(y) entre 0 e "<<image.rows<<":" << endl;
  cin>>p2y;

  if(p1x > p2x){
    int aux;
    aux = p1x;
    p1x = p2x;
    p2x = aux;
  }
  if(p1y > p2y){
    int aux;
    aux = p1y;
    p1y = p2y;
    p2y = aux;
  }

  for (int i = p1y; i < p2y; i++){
    for(int j = p1x; j < p2x; j++){
      val = image.at<Vec3b>(i,j);

      val[0] = 255 - val[0];
      val[1] = 255 - val[2];
      val[2] = 255 - val[2];
      image.at<Vec3b>(i,j)= val;
    }
  }




imshow("janela", image);
waitKey();
return 0;
}
```
#### Original:
![original1](assets/img/saladca.png)

#### Resultado:
![alterada1](assets/img/pdi-regions1.png)


### Exercício 2  

A função deste programa é trocar cada quadrante da imagem pelo seu oposto na diagonal.

#### Código:

```cpp
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace cv;
using namespace std;

int main(int, char**){
  Mat image;
  Vec3b aux;
  image =  imread("img/IMG_0904edit.jpg", CV_LOAD_IMAGE_COLOR);

  if(!image.data){
    cout << "imagem não encontrada" << endl;
  } else{
    resize(image, image, Size(720,480));
  }
  namedWindow("janela",WINDOW_AUTOSIZE);

  for(int i = 0; i < image.rows/2; i++){
    for(int j = 0; j < image.cols/2; j++){
      aux = image.at<Vec3b>(i,j);
      image.at<Vec3b>(i,j) = image.at<Vec3b>(i+image.rows/2,j+image.cols/2);
      image.at<Vec3b>(i+image.rows/2,j+image.cols/2) = aux;

      aux = image.at<Vec3b>(i+image.rows/2,j);
      image.at<Vec3b>(i+image.rows/2,j) = image.at<Vec3b>(i,j+image.cols/2);
      image.at<Vec3b>(i,j+image.cols/2) = aux;

    }
  }



  imshow("janela", image);
  waitKey();
  return 0;
}

```
#### Imagem Original:
![original2](assets/img/IMG_0904edit.jpg)
#### Resultado:
![alterada2](assets/img/pdi-trocaregioes.png)

## 3. Preenchimento de Regiões - Exercícios

No primeiro exercício pede-se encontrar uma forma de rotular os elementos caso existam mais de 255 na imagem. A maneira que utilizei foi dividir os objetos em dois tipos: com e sem buraco. Os objetos com buraco possuem uma cor mais escura que os sem buraco.

Inicialmente foi utilizado o floodfill para percorrer as bordas e remover os objetos que estivessem em contato com elas. Depois foi alterada a cor de fundo para um tom de cinza, assim quando o floodfill detectar um objeto que possua a cor preta dentro dele, já o rotula como "com buraco".

### Exercícios 1 e 2

```cpp
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace cv;
using namespace std;

int main(int argc, char** argv){
  Mat image, mask;
  int width, height;
  int comBuraco, semBuraco;

  CvPoint p;
  image = imread("img/bolhas.png",CV_LOAD_IMAGE_GRAYSCALE);

  if(!image.data){
    std::cout << "imagem nao carregou corretamente\n";
    return(-1);
  }
  width=image.size().width;
  height=image.size().height;

  p.x=0;
  p.y=0;

  //remover bordas
  for (int i = 0; i < height; i += height-1){
    for (int j = 0; j < width; j++){
      if(image.at<uchar>(i,j) == 255){
        p.x = j;
        p.y = i;
        floodFill(image, p, 0);
      }
    }
  }

  for (int i = 0; i < height; i ++){
    for (int j = 0; j < width; j+= width-1){
      if(image.at<uchar>(i,j) == 255){
        p.x = j;
        p.y = i;
        floodFill(image, p, 0);
      }
    }
  }

  //alterar cor de fundo
  p.x = 0;
  p.y = 0;
  floodFill(image, p, 50);

  //buscar objetos com buracos presentes
  comBuraco = 0;
  semBuraco = 0;
  for(int i=0; i<height; i++){
    for(int j=0; j<width; j++){
      if(image.at<uchar>(i,j) == 0 && image.at<uchar>(i-1,j-1) == 255){
      comBuraco++;
      p.x=j;
  		p.y=i;
			floodFill(image,p,150);
			p.x=j-1;
      p.y=i-1;
      floodFill(image,p,150);
	  }
	 }
  }

  for(int i=0; i<height; i++){
    for(int j=0; j<width; j++){
      if(image.at<uchar>(i,j) == 255){
      semBuraco++;
      p.x=j;
      p.y=i;
      floodFill(image,p,200);
    }
   }
  }

  cout<<"Com buraco: "<<comBuraco<<endl;
  cout<<"Sem buraco: "<<semBuraco<<endl;
  cout<<"Total: " <<comBuraco+semBuraco<<endl;
  imshow("image", image);
  imwrite("labeling.png", image);
  waitKey();
  return 0;
}

```  
#### Imagem Original:
![original2](assets/img/bolhas.png)
#### Resultado:
![alterada2](assets/img/pdi-labeling.png)



## 6. Filtragem no domínio espacial II

### Exercício 1 - tiltshift
Este programa reproduz o efeito "tiltshift" em uma imagem.

Uma máscara de borramento é aplicada em uma cópia da imagem original e o usuário do programa escolhe onde as seções borradas serão exibidas. Logo após a execução do programa, a nova imagem é salva em um arquivo.


#### Código

```cpp
#include <iostream>
#include <opencv2/opencv.hpp>
#include <cmath>

using namespace cv;
using namespace std;

double alfa;

int sup_slider = 0;
int sup_slider_max;

int decay_slider = 0;
int decay_slider_max = 100;

int inf_slider = 0;
int inf_slider_max;

Mat image1, image2, blended;

char TrackbarName[50];

double alpha(double x, double l1, double l2, double d){
  return 0.5*(tanh((x-l1)/d) - tanh((x-l2)/d));
}

void on_trackbar_blend(int,void*){
 for (int i = 0; i< image1.rows; i++){
   alfa = alpha(i, sup_slider, inf_slider, decay_slider);
    addWeighted( image1.row(i), alfa, image2.row(i), 1-alfa, 0.0, blended.row(i));
 }
 imshow("tiltshift", blended);
 imwrite("filtrada.jpg", blended);
}


int main(int argvc, char** argv){
  image1 = imread("img/gabi.jpg");
  image1.copyTo(image2);
  image1.copyTo(blended);
  namedWindow("tiltshift", 1);

  sup_slider_max = image1.rows;
  inf_slider_max = image1.rows;

  float media[] = {1,1,1,
                   1,1,1,
                   1,1,1};
  Mat mask(3,3,CV_32F), mask1;
  mask = Mat(3, 3, CV_32F, media);
  scaleAdd(mask, 1/9.0, Mat::zeros(3,3,CV_32F), mask1);
  mask = mask1;

  for(int i = 0; i< 20; i++){
    filter2D(image1, image2, image1.depth(), mask, Point(1,1), 0);
  }
  sprintf( TrackbarName, "Superior %d", sup_slider_max );
  createTrackbar( TrackbarName, "tiltshift",
				  &sup_slider,
				  sup_slider_max,
				  on_trackbar_blend );
  on_trackbar_blend(sup_slider, 0 );

  sprintf( TrackbarName, "Decaimento %d", decay_slider_max );
  createTrackbar( TrackbarName, "tiltshift",
				  &decay_slider,
				  decay_slider_max,
				  on_trackbar_blend );
  on_trackbar_blend(decay_slider, 0 );

  sprintf( TrackbarName, "Inferior %d", inf_slider_max );
  createTrackbar( TrackbarName, "tiltshift",
          &inf_slider,
          inf_slider_max,
          on_trackbar_blend );
  on_trackbar_blend(inf_slider, 0 );

  waitKey(0);
  return 0;
}

```
#### Resultado
![tiltshift](assets/img/pdi-tiltshift.png)
