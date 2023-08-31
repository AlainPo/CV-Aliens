# CV-Aliens

## Мир инопланетян

Много сотен тысяч лет развивалась и процветала жизнь на безымянной экзопланете в звездной системе Сириуса. 
Процветала во многих смыслах, ведь способ питания всех живых организмов — фотосинтез. Технологии же работают за счет преобразования энергии самой яркой звезды в Галактике.

<img src="https://drive.google.com/uc?id=1jV_jASSPMhBStW7HrDqI0VILy65Of296" alt="alien_world" width="500">

Планету населяет раса разумных существ. Жизнь большинства жителей связана с растениями: селекцией, выращиванием, декором. Культ цветов и трав здесь распространен повсеместно.

<img  align="middle" src="https://drive.google.com/uc?id=1r27uvMqkUObpc3jO7Sh2TLS1Smzx3ecp" alt="alien_world" width="500">
*изображения были созданы с помощью <a href="https://github.com/rupeshs/diffusionmagic?ysclid=llkknjn6pv895581605"> StableDiffusionMagic </a>.

Эволюционно жители планеты имеют особенность зрения, которую на Земле назвали бы дальтонизм (протанопия). Характеризуется она отсутствием восприятия оттенков красного, которое отпало в процессе развития за ненадобностью.
Напротив, лучше всего раса различает оттенки желтого и синего, как наиболее контрастные.

<img src="https://bigpicture.ru/wp-content/uploads/2017/01/2027.jpg" alt="color_blindness_types" width="500">

## Описание датасета

Анализ, предобработка и работа над датасетом представлены в Colab блокноте: 
[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)]([https://colab.research.google.com/drive/16dzhZi3azLRqt5TyDG6WIyNNAFsYVez8?usp=sharing])


### Проблематика

>   Что такое image captioning?

Image captioning (рус. “описание изображения”) – это задача создания текстового описания для изображений. Задача расположена на стыке задач компьютерного зрения (CV) и обработки естественного языка (NLP). Большинство image caption-систем используют модель encoder-decoder, где входное изображение кодируется в промежуточное представление информации на картинке, а затем декодируется в последовательность описательного текста

>   Почему над этим работают?

Автоматическое описание изображений может использоваться в виртуальных ассистентах. Эта функция делает использование технологий более комфортным для людей с ограничениями возможностей здоровья
Также функция автоописания изображений может применяться при обработке больших массивов графической информации (эффективный поиск изображений и видео по описанию)

>   Как формулируется задача?

Задача image captioning может формулироваться тремя способами: 

1. Задача аннотирования изображений (annotating) – задача описания входного изображения набором ключевых слов (из заданного словаря). Данную задачу можно рассматривать как задачу классификации с пересекающимися классами
2. Задача поиска описания изображения (retrieval) – выбор наилучшего описания для заданного изображения из описаний, входящих в заданное ограниченное множество. Методы решения данного класса задач – поисковые.
3. Задача генерирования описания изображения (generation) – формирование новых предложений на естественном языке, описывающих заданное изображение. Методы решения данного класса задач – генеративные и гибридные.


### Изучение датасета

>   Что представлено на изображениях?

На изображениях представлены интерьерные фотографии номеров отелей различных сетей
  
>   Сколько объектов в датасете? Сколько уникальных классов? Сбалансирован ли датасет?

В датасете 1124215 фотографий, которые были сделаны в 50000 различных отелях, принадлежащих как минимум 92м гостиничным сетям. Датасет нельзя назвать сбалансированным, поскольку больше половины отелей (27519) и фотографий (596261) не распределены по гостиничным сетям

>   Какие параметры у изображений? Размер фотографий?

Формат всех изображений jpg. Преобладают цветные изображения. Средний размер изображений (высота Х ширина) составляет 233(±1)х350. Есть незначительные выбросы (например, маскимальный размер среди всех изображений выборки — 5248х4320). Также довольно часто встречаются изображения формата 1024х728.


## Обогащение датасета описаниями

Для решения задачи Image Captioning была использована модель ViT-B/32 открытой имплементации [CLIP](https://github.com/openai/CLIP)

### Предобработка

Чтобы добиться соответствия входным параметрам выбранной модели с изображениями проводятся следующие манипуляции:
1. Нормализация интенсивность пикселей по среднеему и среднеквадратичному отклонениям,
2. Изменение размер входных изображений до 224х224,
3. Обрезка изображений по центру по форме квадрата

### Выбор тегов

Следующим этапом стало создание словаря тегов. Для описания изображений были выбраны следующие:
1. "a pool",
2. "a king-size bed",
3. "an amazing view",
4. "a restaurant",
5. "a kitchen",
6. "a balcony",
7. "a shower",
8. "a bathtub",
9. "a sofa",
10. "a tv",
11. "a chair",
12. "a minibar",
13. "a mirror",
14. "two beds",
15. "a table"

С желанием увеличить точность сопоставления тегов и изображений 
* словарь составлялся из *базовых слов* (и некоторых словосочетаний) с тематикой отелей и отдыха (в том числе, чтобы инопланетяне смогли понять, о чем речь :))
* выбирались слова с как можно более *отличающимися смыслами*
* был выбран *небольшой размер словаря*

### Анализ результатов:

1.   Точность присваивания тегов довольно высокая. 
2.   Как и предполагалось, затруднения составляют теги близкие по смыслу (bathtub/shower или two beds/king-size bed) — эти теги были добавлены для проверки гипотезы.
3.   Возникают затруднения с изображениями, на которым нет ни одного из тегов в явном виде, что логично

Приведу пример удачных, на мой взгляд, кейсов

<img src="https://drive.google.com/uc?id=1uMMIfmWhWA-EcXLAJq6HMHpVD2eKSGZV" alt="ex1" height="330"> <img src="https://drive.google.com/uc?id=1G7xZBdvTUVDEqhEKokXUd8KKR85dHgyO" alt="ex2" height="330"> <img src="https://drive.google.com/uc?id=1Xo9h6TRmz_Svs-ngV2qdrNgHuJf1PAOk" alt="ex3" height="330">

И не очень удачных :)

<img src="https://drive.google.com/uc?id=1RvobpjS_vlLBrVeiKfKKKDgRQDUWUJwW" alt="alien_world" height="500"> <img src="https://drive.google.com/uc?id=11DH0KL66yQ2Pj8OOEKNqWhI-uiJjLtGk" alt="alien_world" height="500">

## Изменение изображения при помощи диффузионной модели

После продотворной работы с моделями настало время окунуться в творчество и силами того же <a href="https://github.com/rupeshs/diffusionmagic?ysclid=llkknjn6pv895581605"> StableDiffusionMagic </a> попробовать изменить небольшой набор фотографий так, что он стал приятен и знаком нашим гостям с далекой экзопланеты, но при этом не потерял своих характерных черт и остался узнаваем землянами.

Было выбрано 6 фотографий с различным "наполнением", над изменением которого было интересно поработать: изображения с множеством деталей, противоположной цветовой палитрой и, наоборот, минималистичные, как чистый лист, изображения:

<img src="https://drive.google.com/uc?id=1tf0r7Lxnj-Ht1LL07xDfJlHRiqXgPI8D" alt="img1"> <img src="https://drive.google.com/uc?id=1D-yG_S9GLqMK6kvUd01KfiZro50QdNpf" alt="img1">

<img src="https://drive.google.com/uc?id=1za5Jen20eKhGoZ-J39BfwkLDlr60gp8U" alt="img1"> <img src="https://drive.google.com/uc?id=1Bo0tBctTTLf0E2Ts5MvX28Ih2c6YQPpk" alt="img1">

<img src="https://drive.google.com/uc?id=1wyddUhE-eZY_bR82UyxFZlzhtCvY-hl2" alt="img1"> <img src="https://drive.google.com/uc?id=1KWwiuLo9e6SN936qdbXt5lewhWNylXge" alt="img1">


Вспомним, что инопланетяне живут в зеленом мире и привыкли к множеству разнообразных растений. Этот признак был взят за эталон и исходя из этого строились промпты и настраивались параметры
