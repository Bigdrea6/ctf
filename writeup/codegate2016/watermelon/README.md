# watermelon

# まえがき
明日(2017/2/10)はcodegate 2017だけど, 去年はどんな問題が出たのかなぁ...  
-> watermelon? どんな問題だろう? write-up ﾎﾟﾁｰ  
-> return to dl resolve? 知らないぞ. でもなんかももテクで見た記憶ある  
-> せっかくだし勉強しよう  
という流れでテクニックを勉強しながら解きました.  
なので, 脆弱性を探す過程とかはないです.  

# Write-up
音楽のプレイリストを入力したりできるプログラム.  
modifyするとき, 入力したindexが検査されないので存在しない構造体を書き換えることができるうえ, オーバーフローが可能.  
ここで大きなindexを指定すればstackの任意の場所を書き換えることができる.  
index=101としてやると, musicの16文字目からで$eipを奪うことが可能.  
あとはシェルを起動するだけである.  

## exploit
ただし, systemやexecveが使われておらず, libcも与えられていない. そこで, dl_resolveをしてsystem関数のアドレス解決を行い, シェルを起動する.  
なお, return to dl resolveについては参考として載せたももテクの記事を見てもらいたい.  
ついでに, stack pivotもやったことがなかったのでももテクにのっとり行った. 基本的にコピペ.  

## おぼえがき
ここからは, 解く際に詰まったor今後も詰まりそうなところを自分用にメモ.  
### stack pivot
あまりオーバフローできずにROPが組めない場合などに使うテクニック.  
bss領域に続きのROPをreadしてesp,ebpを差し向けるやつ.  
**bss領域のアドレスを指定する際は, 中間あたりを指定すること**  
bss領域の先頭へpivotすると, systemが呼ばれた際にスタックの頭打ちが原因でSIGSEGVする.  
### Return to dl resolve
アドレス解決を行う仕組みを利用し, system関数などのアドレスを解決させてシェルを起動するテクニック.  

# 参考
- [ROP stager + Return-to-dl-resolveによるASLR+DEP回避](http://inaz2.hatenablog.com/entry/2014/07/15/023406)
