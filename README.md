# RESEARCH-NLP
Данный репозиторий является отчетом о моей научной деятельности.

## 2018/10/30
Прочитал статьи на тему Unsupervised machine translation:

**[2017] Unsupervised Neural Machine Translation** [[paper]](https://arxiv.org/abs/1710.11041), [[code]](https://github.com/artetxem/undreamt)

В данной статье представлен метод для обучения модели нейронного машинного перевода без параллельных корпусов между языками. 
Основные идеи метода:

* **Кросс-язычные векторные представления (Unsupervised Cross-Lingual Embeddings)**.
Сначала обучаем на одноязычных корпусах свои векторные представления, а затем линейным преобразованием отображаем их в общее пространство на основе двуязычного словаря.

* **Кодировщик-декодировщик с вниманием, основанный на двунаправленном GRU**.
В качестве основной модели авторы используют стандартный кодировщик-декодировщик с вниманием со следующими изменениями:
    * Общий кодировщик: авторы используют кросс-язычные векторные представления.
    * Векторные представления в процессе обучения фиксированы.
    * Отдельный декодировщик на каждый язык.

* **Обучение**:
    * `Denoising`. Модель обучается предсказывать свой собственный вход: принимает входную последовательность, 
    кодировать ее и затем декодировать обратно на тот же язык. 
    Авторы добавляют шум к входам, чтобы задача не стало наивной задачей копирования.
    * `Backtranslation`. Недостаточно использовать `denoising`, который переводит только в саму себя, а не между языками. 
    Поэтому используем `backtranslation`: кодируем шумный вход, а затем декодируем его на другой язык.
    Таким образом мы создаем «псевдопараллельный» корпус. 
    * `Итеративное обучение`: чередуем вышеуказанные методы обучения. 

**[2017] Unsupervised Machine Translation Using Monolingual Corpora Only** [[paper]](https://arxiv.org/abs/1711.00043)[[code]](https://github.com/facebookresearch/UnsupervisedMT)

Как и в предыдущей статье, авторы решают задачу машинного перевода без учителя.

* **Модель**
    * Авторы также используют кодировщик-декодировщик с вниманием. 
    Кодировщик является двунаправленным LSTM, а декодер является LSTM.
    * Используется один кодировщик и один декодировщик для всех языков.
    * Векторные представления в процессе обучения не фиксированы.

* **Обучение**:
    * `Denoising`.    
    * `Backtranslation`. 
    * `Итеративное обучение`. 
    * `Adversarial training`. Мы хотим, чтобы распределение выхода 
    кодировщика было одинаковым независимо от входного языка. 
    Для этого авторы обучают нейронную сеть (дискриминатор), который пытается различить 
    кодирования исходного языка от кодирований целевого языка.
    
**[2018] Phrase-Based & Neural Unsupervised Machine Translation** [[paper]](https://arxiv.org/abs/1804.07755)[[code]](https://github.com/facebookresearch/UnsupervisedMT)

Упрощаются предыдущие методы машинного перевода без учителя для родственных языков, используя [`byte pair encoding `](https://arxiv.org/abs/1508.07909) для кодирования предложений.


## 2018/10/01:
Прочитал следующие статьи:

**[2017] Attention Is All You Need** [[paper]](https://arxiv.org/abs/1706.03762), [[code]](https://github.com/jadore801120/attention-is-all-you-need-pytorch). 

Предлагается новая модель `Transformer` для задачи `seq2seq`, которая не использует ни `RNN`, ни `CNN`, а только механизмы внимания. 

**[2017] Regularizing and Optimizing LSTM Language Models** [[paper]](https://arxiv.org/abs/1708.02182), [[code]](https://github.com/salesforce/awd-lstm-lm). 

В статье описываются стратегии регуляризации и оптимизации для задачи 
языковой модели: 
* `DropConnect` для скрытых слоев `LSTM` в качестве метода регуляризации. 
* Последовательности переменной длины для `backpropagation`.
* `Variational dropout` для входных и выходных слоев `LSTM`.
* `Embedding dropout`.
* `Weight tying`. Связывание весов эмбединга энкодера и декодера.

Авторы показывают SOTA результаты на двух датасетах в задаче языковой модели.

**[2018] Universal Language Model Fine-tuning for Text Classification** [[paper]](https://arxiv.org/abs/1801.06146), [[code]](https://github.com/fastai/fastai/tree/master/fastai/text). 

В статье предлагается применить `transfer learning` для задачи классификации текстов:
* В качестве основной модели используется архитектура из предыдущей статьи.
* Модель предобучается на задаче языковой модели на датасете `Wikitext-103` из 103 млн. слов.
* Модель дообучается на данных целевой задачи:
    * В качестве алгоритма оптимизации используется `SGD`.
    * Различные параметры `learning rate` для различных слоев сети 
    (`discriminative fine-tuning`): чем глубже слой, тем больше `learning rate`.
    * `learning rate` обновляется по принципу `1cycle`.
* Полученную модель дообучаем уже на целевой задаче классификации:
    * `Concat pooling`: объединяем скрытое состояние на последнем шаге с 
    максимальным и средним представлением скрытых состояний документа. 
    * Постепенно размораживаем слои начиная с последнего слоя, 
    поскольку она содержит наименее общие знания.
    * Двунаправленный `LSTM`. 

На пяти датасетах авторы достигают существенного улучшения.
