---
layout:     post
title:      Hello GitHub Page
subtitle:    "\"First blog on GitHub Page\""
date:       2018-07-18
author:     PGJ
header-img: img/post-bg-2015.jpg
catalog: false
tags:
    - Blog
---

--运行yesno例子

0. 说明
egs/yesno/s5目录下run.sh为示例运行总脚本，本文档记录对此脚本拆解运行的详细过程。

1. 下载并解压数据
wget http://www.openslr.org/resources/1/waves_yesno.tar.gz

解压数据至 egs/yesno/s5目录。

2. 准备训练与测试数据

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

3. 准备字典数据

$sh local/prepare_dict.sh

输出目录 data/local/dict，输出文件：

lexicon_words.txt -- 发音词典

lexicon.txt -- 发音词典，包括SIL

nonsilence_phones.txt -- 非静音音素。音素集phones.txt除去silence_phones.txt中的所有音素，不能包含<eps>.

silence_phones.txt -- 静音音素。写入sil

optional_silence.txt -- 可选静音音素。写入sil

4. 

$utils/prepare_lang.sh --position-dependent-phones false data/local/dict "<SIL>" data/local/lang data/lang
