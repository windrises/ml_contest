整理一下最近的工作，附上代码和准备的ppt

情感分析比赛源数据是Stanford整理的那个imdb数据，原数据是25k train + 25k test + 50k unsup，区别在于课程数据里去掉了25k的test，然后从train里抽出了5k的数据当做test

这里简单做了几个实验，准确率都在90%附近，没做进一步的调参，不过估计难有比较大的提升(代码没有经过认真整理，所以可能会有点乱)：

- TF-IDF是最简单也是运行速度最快的模型，分类器可以用SVM、LogReg、NaiveBayes、MLP等等，不过效果应该差不多

- Deep_learning，这里用Keras写了一个CNN的模型。在做词嵌入时只是简单用了texts_to_sequences，并且为了降低数据维度，只取了部分词频高的单词，而且还对句子做了截断，这都是可能的优化方向。还可以考虑换成词嵌入加深度模型，比如Word2vec或Glove。除了CNN，Keras还提供了LSTM，CNN+LSTM，Bi_LSTM等模型，我都试过了，由于数据量比较小，效果差别不大 当然还可以尝试加入Attention

- Sentence_embedding 是直接对句子做了嵌入，优点在于不用考虑上面提到的维度过大的问题，比单纯地对单句所有单词做向量平均要好，还可以利用上unsup数据。然后可以直接拿SVM，LR或者MLP等作分类器

- N-gram 主要是参考了fast-text的做法，实验后发现n设为2比较好

## 一些调研：

state of the art的算法错误率在5%左右，但是要么没有源代码，要么难以复现。而且只有比较少的论文利用上了unsup数据。比如：

- [Universal Language Model Fine-tuning for Text Classification](https://arxiv.org/abs/1801.06146)，18年，错误率只有4.6%(https://github.com/fastai/fastai/tree/master/courses/dl2/imdb_scripts 代码维护得很差。没有GPU的话，单pre-train的一个epoch就需要十多个小时，放弃)

- [Adversarial Training Methods for Semi-Supervised Text Classification](https://arxiv.org/abs/1605.07725)，17年，提出了Adversarial模型，半监督，用上了unsup数据，提供了用TF写的源代码以及参数(https://github.com/tensorflow/models/tree/master/research/adversarial_text 试了一下发现pre-train需要100k个steps，放弃)

- [Supervised and semi-supervisedtext categorization using LSTM for regionembeddings](https://arxiv.org/abs/1602.02373)，16年，作者发了一系列论文，提出了ConText模型，不过代码是用C++和CUDA写的(https://github.com/riejohnson/ConText)

- [Learned in Translation: Contextualized Word Vectors](https://arxiv.org/abs/1708.00107)，17年，提出了CoVe模型(https://github.com/salesforce/cove)

- [Distributed Representations of Sentences and Documents](https://arxiv.org/abs/1405.4053)，14年，作者是Mikolov，也是Word2vec的作者，这里提出了Doc2vec做句子嵌入，提供了C++代码，论文里说错误率在7.4%。还有论文说用两层MLP做分类器可以把准确率提高到94.5%。不过别人用python的gensim包复现时发现不管怎么优化，准确率最多90%左右(https://github.com/RaRe-Technologies/gensim/blob/develop/docs/notebooks/doc2vec-IMDB.ipynb)

还有一些专在imdb数据集上做sentiment analysis的论文，比如：

- Sentiment Analysis with Deeply Learned Distributed Representations of Variable Length Texts，Stanford的，论文里说用句子嵌入+两层MLP准确率能达到94.5%

- Deep learning for sentiment analysis of movie reviews，也是Stanford的，没细看

还有一些综述性的博客：

https://blog.paralleldots.com/data-science/breakthrough-research-papers-and-models-for-sentiment-analysis/

http://nlpprogress.com/english/sentiment_analysis.html

等等

---

课上看了一下别的组的方法，其实大家的思路和方法都差不多。不少组很重视预处理和集成学习，这是值得学习的。