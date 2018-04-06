
<!--
You can use HTML elements in Markdown, such as the comment element, and they won't be affected by a markdown parser. However, if you create an HTML element in your markdown file, you cannot use markdown syntax within that element's contents.
-->

## 2. Manipulação de pixels - Exercícios
### Exercício 1  

#### Código - regions.cpp
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

#### Resultado:
![alterada1](assets/img/pdi-regions1.png)


### Exercício 2  

#### Código - trocaregioes.cpp

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
#### Imagem Original| Resultado:
![alterada1](assets/img/pdi-trocaregioes.png)| [alterada2](assets/img/IMG_0904edit.jpg) 
#### Resultado:
