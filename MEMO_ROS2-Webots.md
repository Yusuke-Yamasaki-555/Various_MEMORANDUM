# Webots＆ROS2連携に関するメモ
調査対象：[cyberbotics/webots_ros2](https://github.com/cyberbotics/webots_ros2)<br>
参考：[cyberbotics/webots_ros2,Wiki](https://github.com/cyberbotics/webots_ros2/wiki)<br>
　　　[Webots Reference Manual](https://cyberbotics.com/doc/reference/index)<br>
<br>
## Path
### sampleのパス
/opt/ros/foxy/share/webots_ros2_universal_robot/ <br>
### Webotsのパス
/usr/local/webots/ <br>
### Webots既存のロボットモデル（.protoファイル）のパス
/usr/local/webots/projects/robots/ <br>
<br>
## Webots & ROS2連携方法調査記録
[公式Wiki(ちゃんと読もう！)](https://github.com/cyberbotics/webots_ros2/wiki/Tutorial-Creating-a-Custom-Cpp-Plugin) <br>
（参考例：webots_ros2_universal_robot/）<br>
・（参考例ロボット：UR5e） <br>
　・URDFやyamlに記載されているjoints名は、Webots側で既に決められている。それに従う。 <br>
　　・sampleのロボットを使いたいなら、[Webots User Guide, Robots](https://cyberbotics.com/doc/guide/robots?version=R2022a)を見ればわかる。 <br>
　・.protoファイルが、Webotsでのモデル記述ファイル（ROSでいうURDFと同じ）。 <br>
　　・恐らく、ロボットのjointやらは全て.protoファイルに記述されている。そこを見れば全パラメータがわかる。 <br>
   ・URDFのほうには、jointをprotoに合わせるだけでいい感じ？センサ類もwebots側にあるから、それをURDFが合わせるだけで良さそう？ <br>
　・では、multirobot_launch.pyでの各ロボットのcontrollerは何処？sampleのパスには、launch - resource - worldsしかなかった。 <br>
　　・controllerは、パッケージ名（webots_ros2_universal_robot）と同じフォルダ名の可能性？（tutorialのmy_package然り） <br>
　　・[GitHubのソース](https://github.com/cyberbotics/webots_ros2/tree/master/webots_ros2_universal_robot)でわかるじゃん <br>
　・連携のコードにおいて、Pythonでの関数やらライブラリはソースを読めばわかるが、C++は？ <br>
　　・公式Wiki(上記)をあさりたまえよ <br>
     ・全部、全部書いてあった。後はサンプルコードにコメントを一つずつ付けていきたいね。<br>
・universal_robotについて<br>
　・`follow_joint_trajectory_client.py`でactionの内容を記述している。importされる。classだけを提供するコード。<br>
　・`abb(or ur5e)_controller.py`でrobotのコントロール（座標(point)やjointの名前をメッセージの要素に入れている。恐らく配列）をしている。処理内容というよりパラメータを記述している。ここにactionのclient口も書かれており、処理内容は`follow_joint_trajectory_clinet.py`のクラスをimportすることで対処している。<br>
　・使うロボットに関してはLaunchでURDFやパラメータを記述したyamlを指定しているはず。<br>
　・URDFの`~_description.urdf`では、ロボットの名前は最初に宣言されているのみである。そのため、controlはここを読んでいるのかもしれない。Wikiの方にも書かれていたが、使うロボットの名前をパラメータに記述するときは元のと同じにしてね的なことが書かれていたから、そういうことなんだと思う。（要調査）<br>
<br>
**以下、10/2時点の調査結果（メモ程度）**
<br>
・#include <webots/Node.hpp>、#include <webots/Supervisor.hpp>が重要。この２つからWebotsのAPIにアクセスする。これはwebots_nodeから提供される。<br>
　・Cppの場合、webots_ros2_driver::PluginInterfaceを継承するなど、Wikiにあるように設定すれば使える（はず）。記述はどうすればいいのかが問題（書いてみなきゃわからん）<br>
・teslaのサンプルの場合、自動車用のAPIを使っている。<br>
・ROBOTIS_OP2を使う場合、RotationalMotorやAccelerometer、PositionSensorなどのロボットが搭載している機能に対応したAPI（API名まんま）を呼び出して使えば良いだろう。([ROBOTIS' Robotis OP2: Reference](https://cyberbotics.com/doc/guide/robotis-op2?version=R2022a))<br>
・いまわかってないのは、URDFでの<joint>内で宣言されている<state_interface>と<command_interface>の決め方。positionだけじゃなく角速度とかもほしい。<br>
　・これはpositionを微分してやれば良いのでは？となるとWebotsからシミュレーション内時間を得る必要がある。ROS2連携によるリアルタイム性も関係してくる（はず）。<br>
<br>
   
## ROS2関連の調査記録

## Webots関連の調査記録

## その他の調査記録
・コード内にある"future"関数は、C++やPythonに標準で搭載されている関数である。どちらも、非同期処理を実現するためのクラスとなっている。[このプログラム](https://demura.net/education/lecture/21725.html)では、**Futureが実行されているときは、サーバーからのレスポンスをチェックし、レスポンスがあったらその内容を端末に表示する。**としている。sesrviceとかの非同期処理に使われ、特にserviceだとclient側で使用されているのが見られる。[このプログラム](https://qiita.com/NeK/items/9d15487d4853638394a3#%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%A0callback%E9%96%A2%E6%95%B0%E3%81%AB%E3%82%88%E3%82%8Btimer%E5%87%A6%E7%90%86%E3%81%A8%E9%9D%9E%E5%90%8C%E6%9C%9F%E5%87%A6%E7%90%86)だと、ROS風な記述で用いたspin系関数である"spin_until_future_complete"の代わりのところに使われている。<br>
　・参考Documentation（[Python: Future](https://docs.python.org/ja/3/library/asyncio-future.html), [C++: std::future](https://cpprefjp.github.io/reference/future/future.html) ) 
<br>
<br>
