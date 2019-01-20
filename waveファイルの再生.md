---
name: wavファイルの再生
number:200 
wip: true
---

# WAVEファイルの再生

### すること
- 音そのものの再生
- wavファイルから波形データを取得
- wavファイルの音を再生

### 実行環境
- データ  
  声優統計コーパスを利用します.
- 実行環境  
  - Mac(High Sierra)
  - python3.7
  - Jupyter lab

まずwavファイルから音を再生します  
初心者だったので,音はどういうデータとして扱われているかから調べました.

調べた結果,音声データは(シンプルには)1次元の配列として扱われていることがわかりました.
配列のi番目の要素の大きさが時刻iでの音の大きさを表します.

実際に,単純な正弦波を再生します.

```python
import numpy as np
import IPython.display as ipd

rate = 8000
length = 1
t = np.arange(0, length, 1 / rate)
amp = 2.0
freq = 440
audio = amp * np.sin(2 * np.pi * freq * t)

ipd.Audio(audio, rate=rate)
```

次に実際のwavデータを読み込みます.  
データを読み込む時は `scipy` の`scipy.io.wavfile` を使います.

```python
from scipy.io import wavfile
voice_path = "../voice-statistics/assets/data/tsuchiya_happy/tsuchiya_happy_001.wav"
fs, voice = wavfile.read(voice_path)

ipd.Audio(voice, rate=fs)
```

`fs`はサンプリング周波数,`voice`は音声データの一次元配列です.
`voice`のサイズからフレーム数がわかり,フレーム数/ サンプリング周波数で再生時間もわかります.




__注意__  
`read`時に`WavFileWarning: Chunk (non-data) not understood, skipping it. WavFileWarning)`が出ることがあります.
おそらくwavのサンプリングレートとデータ本体以外のチャンクが捨てられたため,警告が出たのではないかと思います.

wavファイルのチャンクについては[ここ](http://sky.geocities.jp/kmaedam/directx9/waveform.html)が参考になりました.

__注意2__  
wavファイルにはステレオとモノラルの2つが(主に)あります.
ステレオは二次元配列,モノラルは一次元配列になります.
`read`も`ipd.Audio`もステレオ,モノラル両方対応しています.
ただし,ステレオの場合,型が異なっており,転置が必要になります.

```python
fs, stereo_audio = wavfile.read('./audios/signal.wav')
ipd.Audio(stereo_audio.T, rate=fs)
```

統計コーパスはモノラルなので特に意識する必要はありません.
