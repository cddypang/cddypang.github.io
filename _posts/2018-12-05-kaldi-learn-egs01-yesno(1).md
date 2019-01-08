---
layout:     post
title:      kaldi-learn-egs01-yesno(1)
subtitle:   “数据准备，生成L.fst G.fst”
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

准备数据，创建data目录保存输出结果；

data目录中创建 local、test_yesno、train_yesno 三个子目录；

输出结果文件说明：

data/local/waves_all.list -- 所有音频文件列表

data/local/waves.test -- 测试文件名列表

data/local/waves.train -- 训练文件名列表

data/local/test_yesno_wav.scp -- 不带扩展名的测试文件名与文件路径(相对路径)对应列表

data/local/train_yesno_wav.scp -- 不带扩展名的训练文件名与文件路径对应列表

data/local/test_yesno.txt -- 测试音频文件名与音频内容对应列表

data/local/train_yesno.txt -- 训练音频文件称与音频内容对应列表

data/local/lm_tg.arpa -- n-gram语言模型格式文件，该文件应有训练工具训练生成

--n-gram语言模型简单介绍：

https://blog.csdn.net/asrgreek/article/details/81979194

https://blog.csdn.net/lv_xinmy/article/details/8595561

https://blog.csdn.net/zhoubl668/article/details/7759042

https://blog.csdn.net/qq_27825451/article/details/83181500

--arpa2fst原理介绍

https://blog.csdn.net/yutianzuijin/article/details/78756130

分别在data/test_yesno data/train_yesno目录根据.scp .txt文件内容生成utt2spk和spk2utt文件

data/{train_yesno，test_yesno}/utt2spk -- 指明某段发音是哪个人说的（注意一点，说话人编号并不需要与说话人实际的名字完全一致——只需要大概能够猜出来就行。）

data/{train_yesno，test_yesno}/spk2utt -- 与utt2spk内容相同，关系反向，表明说话人编号与音频文件列表的关系

###### 3.准备发音音素字典数据

$sh local/prepare_dict.sh

输出目录 data/local/dict，输出文件：

data/local/dict/lexicon_words.txt -- 不包括静音的有效语音词汇的发音词典

data/local/dict/lexicon.txt -- 包括静音SIL的发音词典

data/local/dict/nonsilence_phones.txt -- 非静音音素列表。音素集文件input/phones.txt中除去silence_phones.txt中的所有音素，不能包含<eps>.

data/local/dict/silence_phones.txt -- 静音音素。写入sil

data/local/dict/optional_silence.txt -- 可选静音音素。写入sil

###### 4.构造字典fst模型(L.fst)

$utils/prepare_lang.sh --position-dependent-phones false data/local/dict "<SIL>" data/local/lang data/lang

详细阅读utils/prepare_lang.sh代码，此脚本处理东西较多；

1> cp $srcdir/lexiconp.txt $tmpdir/lexiconp.txt

2> paste -d' ' $tmpdir/phones $tmpdir/phones > $tmpdir/phone_map.txt

生成data/local/lang/phone_map.txt;

3> share_silence_phones false;

   **此处大致是说HMM状态共享及pdf-class(混合高斯模型)分裂问题，自己没看懂，留着以后慢慢学习!!!**

3-1> cat $srcdir/{,non}silence_phones.txt | utils/apply_map.pl $tmpdir/phone_map.txt > $dir/phones/sets.txt

    输出data/lang/phones/sets.txt，内容为包括静音的因素列表(SIL,Y,N)；

3-2> cat $dir/phones/sets.txt | awk '{print "shared", "split", $0;}' > $dir/phones/roots.txt

    输出data/lang/phones/roots.txt，内容为不同因素间的HMM状态共享及决策树分裂配置列表，具体介绍参看下面4.1部分的图片内容；



####### 4.1

输入相关文件格式：

lexicon.txt -- 字典文件，格式为：word phone1 phone2 ... phoneN

lexconp.txt -- 带概率的字典文件，概率值为每个词出现的概率，格式为：word pron-prob phone1 phone2 ... phoneN
               
silence_phones.txt/nonsilence_phones.txt -- 静音/非静音音素，每一组代表一行相同的base phone，但可能有不同的重音或音调的音素，例如a a1 a2 a3 a4

optional_silence.txt -- 包含一个单独音素作为字典中默认的静音音素

extra_questions.txt -- (用于构建决策树的问题集，可以为空)包含多组的音素，每一组音素包含相同的重音或音调；也有可能是一致表示非语音的静音/噪音音素。这可以用于增加自动生成问题的数量。(注意：在自动生成问题的程序对包含重音/声调的音素的处理方式跟普通音素处理方式一样，实际上程序并不知道哪些是带重音/声调的音素，它只是将这些音素当成普通的音素进行单独处理。)

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-01.png" title="01.png"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-02.png" title="02.png"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-03.png" title="03.png"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-04.png" title="04.png"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-05.png" title="05.png"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-06.png" title="06.png"/>

<img src="/img/kaldi-learn/kaldi-prepare_lang_sh-description-07.png" title="07.png"/>


**注意：MAC环境中因没有X11环境，可能导致png，jpg图片显示不出来。MAC环境下使用fstdraw命令画图可以输出svg矢量图**

**fstdraw --isymbols=phones.txt --osymbols=words.txt L.fst | dot -Tsvg -o L.svg**


###### 5.构造语言模型(G.fst)

arpa2fst --disambig-symbol=#0 --read-symbol-table=$test/words.txt input/task.arpabo $test/G.fst

最终生成G.fst文件。

至此，数据准备阶段基本完成，下一阶段开始进行音频特征提取处理。
