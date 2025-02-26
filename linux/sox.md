- Table of Content  
{:toc}

# SoX

* Wavファイル情報を表示  
soxi hoge.wav
* Wavファイルを結合  
sox hoge1.wav hoge2.wav result.wav
* Wavファイルを分割  
(例) 3秒単位で分割  
`sox hoge.wav splited.wav trim 0 3 : newfile : restart`  
(例) 最初の 3秒だけ切り出し(分割)  
`sox hoge.wav splited.wav trim 0 3`
* Wavファイルの形式を PCM に変換  
sox  hoge.wav -t wav -b 16 hoge_pcm.wav
* Wavファイルの形式を PCM から u-law に変換(8KHz, 8bit)  
sox pcm.wav -t wav -e mu-law -r 8k -b 8 ulaw.wav
* ステレオの Wavファイルの左右チャネルを合成してモノラルの Wavファイルに変換  
sox stereo.wav -c 1 mono.wav
* ステレオの Wavファイルから Lまたは Rチャネル(チャネル0 または 1)だけを取り出し、モノラルの Wavファイルを出力する  
Lチャネル: sox stereo.wav -c 1 left.wav remix 1  
Rチャネル: sox stereo.wav -c 1 right.wav remix 2
* モノラルの Wavファイル 2つからステレオの Wavファイルを作成する  
sox -M left.wav right.wav -c 2 stereo.wav
* ステレオWavファイルの左右を入れ替える  
sox stereo.wav reverse.wav swap  
* Wavファイルを RAW に変換  
`sox hoge.wav -t raw hoge.raw`
* RAWなPCMファイルを Wavファイルに変換  
`sox -t raw -b 16 -r 8k -c 2 -e signed hoge.raw -t wav hoge.wav`
* mp3ファイルを u-law の Wavファイルに変換する  
sox -V3 input.mp3 -t wav -e mu-law -r 8k output.wav
* ノイズ除去  
sox noisy.wav -n noiseprof noise.prof  
sox noisy.wav clean.wav noisered noise.prof 0.21
* 対応フォーマットを一覧表示  
sox --help-format all
