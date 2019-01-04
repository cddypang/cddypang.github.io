---
layout:     post
title:      kaldi-learn-egs01-yesno
subtitle:    
date:       2018-12-05
author:     PGJ
header-img: img/post-bg-2015.jpg
catalog: false
tags:
    - kaldi
---

**运行yesno例子**

https://blog.csdn.net/nsh119/article/details/81587462

###### 0.说明
egs/yesno/s5目录下run.sh为示例运行总脚本，本文档记录对此脚本拆解运行的详细过程。

###### 1.下载并解压数据
wget http://www.openslr.org/resources/1/waves_yesno.tar.gz

解压数据至 egs/yesno/s5目录。

###### 2.准备训练与测试数据

$sh local/prepare_data.sh waves_yesno

输出结果文件：

waves_all.list -- 所有音频文件列表

waves.test -- 测试文件名列表

waves.train -- 训练文件名列表

test_yesno_wav.scp -- 不带扩展名的测试文件名与文件目录对应列表

train_yesno_wav.scp -- 不带扩展名的训练文件名与文件目录对应列表

test_yesno.txt -- 测试文件名称与音频内容单词对应列表

train_yesno.txt -- 训练文件名称与音频内容单词对应列表

lm_tg.arpa -- n-gram语言模型格式文件

--n-gram语言模型简单介绍：https://blog.csdn.net/lv_xinmy/article/details/8595561


分别在data/test_yesno data/train_yesno目录根据.scp .txt文件内容生成utt2spk和spk2utt文件

utt2spk -- 指明某段发音是哪个人说的（注意一点，说话人编号并不需要与说话人实际的名字完全一致——只需要大概能够猜出来就行。）

spk2utt -- 与utt2spk内容相同，关系反向，表明说话人编号与音频文件列表的关系

###### 3.准备字典数据

$sh local/prepare_dict.sh

输出目录 data/local/dict，输出文件：

lexicon_words.txt -- 发音词典

lexicon.txt -- 发音词典，包括SIL

nonsilence_phones.txt -- 非静音音素。音素集phones.txt除去silence_phones.txt中的所有音素，不能包含<eps>.

silence_phones.txt -- 静音音素。写入sil

optional_silence.txt -- 可选静音音素。写入sil

###### 4.构造字典fst模型(L.fst)

$utils/prepare_lang.sh --position-dependent-phones false data/local/dict "<SIL>" data/local/lang data/lang

####### 4.1

输入相关文件格式：

lexicon.txt -- 字典文件，格式为：word phone1 phone2 ... phoneN

lexconp.txt -- 带概率的字典文件，概率值为每个词出现的概率，格式为：word pron-prob phone1 phone2 ... phoneN
               
silence_phones.txt/nonsilence_phones.txt -- 静音/非静音音素，每一组代表一行相同的base phone，但可能有不同的重音或音调的音素，例如a a1 a2 a3 a4

optional_silence.txt -- 包含一个单独音素作为字典中默认的静音音素

extra_questions.txt -- (用于构建决策树的问题集，可以为空)包含多组的音素，每一组音素包含相同的重音或音调；也有可能是一致表示非语音的静音/噪音音素。这可以用于增加自动生成问题的数量。(注意：在自动生成问题的程序对包含重音/声调的音素的处理方式跟普通音素处理方式一样，实际上程序并不知道哪些是带重音/声调的音素，它只是将这些音素当成普通的音素进行单独处理。)

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-01.jpg" title="01.jpg"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-02.jpg" title="02.jpg"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-03.jpg" title="03.jpg"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-04.jpg" title="04.jpg"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-05.jpg" title="05.jpg"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-06.jpg" title="06.jpg"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-07.jpg" title="07.jpg"/>


**注意：MAC环境中因没有X11环境，可能导致png，jpg图片显示不出来。MAC环境下使用fstdraw命令画图可以输出svg矢量图**

**fstdraw --isymbols=phones.txt --osymbols=words.txt L.fst | dot -Tsvg -o L.svg**


###### 4.构造字典fst模型(L.fst)



