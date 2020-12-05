# Models(classification)
  - ResNet
  - ResNext

## ResNet
![](https://github.com/kiru883/Courses/blob/master/CV%20notes/images/resnet.PNG)

- **Основная мысль** - res. block(fig. c) вместо того чтоб аппроксимироваться к H(x)("идеальный" feature map), с помощью классических методов, апроксимируемся "остатками". 
Н(х) = F(x) + x, где х это инпут, F(x) это остаток, каждый блок использует пропуск соединения(shortcut), мое интуитивное понятие, ассоциация с град. бустингом, 
там мы тоже генерим ОСТАТКИ градиентов лосса и затем суммируем их с предиктом на начальном предикторе, используем BN+ReLu после conv слоев.
- **Fig. A** - основные архитектуры реснет сетей, конв блоки имеют same паддинг, размеры feature map'ов считались исходя из дефолтного размера входного изобр. - **224х224**
- **Fig. C** - основной блок этой сетки, bottleneck, их  2 вида, первый для сетей до 34 слоев, второй для сеток после 34 слоев, линия сбоку - shortcut(identity mapping). 
В настоящих реализациях после конв. используется пакетная нормализация и активация.
- **Fig. B** - пример 34-й архитектуры, разными цветами обозначены блоки с РАЗНЫМ размером feature map'a, используется также 2 вида shortcut'a, первый - классический, обычная передача инпута, второй(штрих) для изменения размера feature map'a, изменять он его может двумя разными методами: А - непараметр. метод, проходимся пулингом 1х1 с страйдом 2, теперь размер feature map'ов изменился но количество каналов такое же, добавляем(18, 34) нулевые/убираем(>34) feature map'ы. В - параметрический метод, он лучше, проходимся конв. 1х1 с страйдом 2(к примеру на изображении было 64 канала, прошлись 128 ядрами с страйдом 2), кол-во фильтров при этом увеличиличивает(18/34 архит.)/уменьшает(>34 архит.)(см. архитектуру) кол-во каналов на след. блоке

Количество параметров
  ![](https://github.com/kiru883/Courses/blob/master/CV%20notes/images/resnetP.PNG)

Ссылки
  - https://arxiv.org/pdf/1512.03385.pdf - ориг
  - https://arxiv.org/pdf/1603.05027.pdf - тут утверждают, что relu лучше применять ток к остаткам
  - https://github.com/keras-team/keras-applications/blob/master/keras_applications/resnet50.py - реализация от keras team
  - https://towardsdatascience.com/intuition-behind-residual-neural-networks-fa5d2996b2c7
  - https://neurohive.io/ru/vidy-nejrosetej/resnet-34-50-101/
  
  
  
## ResNext
  ![](https://github.com/kiru883/Courses/blob/master/CV%20notes/images/resnext.PNG)
  
  - **Основная мысль** -  в использовании grouped convolution(нескольких трансформаций), это приводит к тому, что веса на фильтрах в слое grouped convolution менее коррелируют, это приводит к **более "разным" на выходе feature map'ам(несущих более разную инфу)**, хотя параметров у модели при этом даже чуть меньше чем у resnet'а, архитектура не меняется по сравнению с ресНетом(размер входного изобр. 224х224, после конволюций используем BN+ReLu), однако меняется топология bottleneck'a(плюс еще используем BN к остаткам перед добавлением к инпуту), в этом есть еще несколько плюсов:
    1. **Не меняется кол-во параметров** на одном bottleneck'е, кол-во параметров на resnet'овском ботлнеке: 256 · 64 + 3 · 3 · 64 · 64 + 64 · 256 ≈ 70k,
    на resneXt'овском - C · (256 · d + 3 · 3 · d · d + d · 256), где С - это параметр его ботлнека(cardinality, кол-во трансформаций)
    2. Возможность распаралелить модель из-за grouped convolution(я с этим не сталкивался)
  - **Fig. A** - Сама архитектура, cлева ResNet, справа ResNext
  - **Fig. B** - Bottleneck модели, они эквивалентны, имеют параметр cardinality, кол-во трансформаций, в реализации использовался С вариант
  - **Fig. C** - Скоры на imagenet-1k
 
 
  Мои какие никакие визуализации:
    ![](https://github.com/kiru883/Courses/blob/master/CV%20notes/images/ResNextBlockVisual.PNG)
    ![](https://github.com/kiru883/Courses/blob/master/CV%20notes/images/rexnextblock.PNG)
    
  - **Fig. D** - Визуализация почему bottleneck'и на fig. B эквивалентны
  - **Fig. E** - Визуализация обычной конволюции у resnet'a(сверху) и grouped convolution у resneXt'a(снизу)
    
Ссылки
  - https://arxiv.org/pdf/1611.05431.pdf - ориг
  - https://towardsdatascience.com/a-comprehensive-introduction-to-different-types-of-convolutions-in-deep-learning-669281e58215 - почему и как про grouped convolution
  - https://pytorch.org/docs/stable/_modules/torchvision/models/resnet.html#resnext50_32x4d - pytorch'евская имплементация 
  - https://medium.com/@14prakash/understanding-and-implementing-architectures-of-resnet-and-resnext-for-state-of-the-art-image-cc5d0adf648e - неплохое введение

## Models(Segmentation)
  - Unet
    - https://arxiv.org/pdf/1505.04597.pdf
