# AJACSadvanced三島3

### 公式ウェブページ
[統合データベース講習会AJACSadvanced(AJACSa)三島3](http://bit.ly/161215NIG/)

### 開催概要
JSTのバイオサイエンスデータベースセンター(NBDC)では、統合データベース講習会AJACSを全国の大学や研究所で開催しております。DBCLSの研究員もその講師を務めておりますが、中上級のadvancedな内容の講習会として今回、非モデル生物のRNA-seqデータ解析で活躍するTrinityを使った講習会を遺伝研研究会「次世代モデル生物におけるゲノム情報利用ワークショップ」と連続開催で行います。


### 日時
2016年12月16日(金)

### 会場
国立遺伝学研究所 宿泊棟会議室 

### 参加対象者

− 非モデル生物でNGSデータ解析環境を作りたい方
- de novo transcriptome assembly のツールTrinityを使ってみたい方
- 次世代シーケンサーDRY解析教本Level1を一通りマスターした程度のUNIXのコマンドラインが使える方
- Mac所有者もしくはLinuxが使える方(講習はMacで。各自コンピュータを持ち込んでください。貸出はありません)
- NGSデータ解析の``プロ``でない方。講習する方が困りますので…


### プログラム
 
13:00～16:00 「pitagora-galaxyを使ったデータ解析環境の構築とTrinityによる Transcriptome assembly」  
坊農 秀雅  
(情報・システム研究機構ライフサイエンス統合データベースセンター)

### Pitagora-galaxy

#### Galaxyとは
> [生物医学研究のデータ解析ために無償で利用できるWebベースの統合データ解析環境](http://www.pitagora-galaxy.org/about)

#### とりあえず、使ってみる
インストールすること無く、お試しでウェブブラウザ上で

http://try.pitagora-galaxy.org/galaxy/

実用的?

#### そこで、仮想環境
http://www.pitagora-galaxy.org/download

参考: [統合TV「Pitagora Galaxyを使ってマウス操作だけでRNA-seq解析をする」](http://doi.org/10.7875/togotv.2016.054)

【課題1】 NGS tools -> FASTQC を実行してみましょう。
- 気になるFASTQデータをSRAからダウンロードしましょう -> [DBCLS SRA](http://sra.dbcls.jp/)
- 左上のアップロードアイコンにデータをアップロード
- 
### Trinity

- [Trinity](https://trinityrnaseq.github.io/)は、よく使われている de novo transcriptome assembly のツール。

- Citation: Nat Biotechnol. 2011 May 15;29(7):644-52. [doi:10.1038/nbt.1883](http://doi.org/10.1038/nbt.1883)

#### インストール

- MacOSX + [homebrew](http://brew.sh/index_ja.html)だと以下のコマンドでインストール完了
```brew install -v Trinity```

- 講習会の時点での(homebrewでインストールできる)最新バージョンは```2.3.2```

#### 本体実行

- ペアエンドの場合

```Trinity --seqType fq --left hoge_1.fq.gz --right hoge_2.fq.gz --max_memory 16G --CPU 4```

- シングルエンドの場合

```Trinity --seqType fq --single fuga.fq.bz2 --max_memory 16G --CPU 4```

指定するFASTQファイルは(gzip, bzip2)圧縮されていても大丈夫の模様。```--max_memory```で使用メモリ上限、```--CPU```で使用するCPU数を指定。

- 出力結果は、転写単位ごとの塩基配列のFASTA形式ファイル。実行したディレクトリの下に```trinity_out_dir```というディレクトリが作成され、その中の```Trinity.fasta```というファイルがそれ。
- 参考: [ぼうのブログ: Trinityで転写産物を定量する](http://bonohu.jp/blog/2016/05/09/trinity-on-macosx/)

#### 発現定量

- [```align_and_estimate_abundance.pl```](https://github.com/trinityrnaseq/trinityrnaseq/wiki/Trinity-Transcript-Quantification) を使うと、転写量を見積もって定量できる。
- RSEM(RNA-Seq by Expectation-Maximization), bowtie2を追加インストールする必要がある
	- RSEM: http://deweylab.github.io/RSEM/ からダウンロードの上、```make```して```make install```
	- bowtie2:  ```brew install -v bowtie2```でインストール可能
- 以下のようなスクリプト(```align_and_estimate_abundance.sh```)で実行可能

```align_and_estimate_abundance.sh
/usr/local/Cellar/trinity/2.3.2/util/align_and_estimate_abundance.pl \
--thread_count 4 \
--transcripts trinity_out_dir/Trinity.fasta \
--seqType fq \
--left hoge_1.fq.gz --right hoge_2.fq.gz \
--est_method RSEM \
--aln_method bowtie2 \
--trinity_mode \
--prep_reference --output_dir rsem_outdir
```

- 参考: [ぼうのブログ: Trinityで転写産物を定量する](http://bonohu.jp/blog/2016/05/27/trinity-transcript-quantification/)

#### トラブルシューティング
- 多くはメモリ不足。どうしても実行できない場合は、遺伝研スパコンなど大きなメモリ空間が使えるマシン利用を検討
-  [ぼうのブログ: single end readsでTrinityがコケる](http://bonohu.jp/blog/2016/08/29/problem-assembling-single-end-reads/)
