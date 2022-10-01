# Webots＆ROS2連携に関するメモ
(RobotisOp2のwalk-controllerのソースコードに、色々コメントをつけてある) <br>
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
<br>
・#include <webots/Node.hpp>、#include <webots/Supervisor.hpp>が重要。この２つからWebotsのAPIにアクセスする。これはwebots_nodeから提供される。<br>
　・Cppの場合、webots_ros2_driver::PluginInterfaceを継承するなど、Wikiにあるように設定すれば使える（はず）。<br>
・teslaのサンプルの場合、自動車用のAPIを使っている。<br>
・ROBOTIS_OP2を使う場合、RotationalMotorやAccelerometer、PositionSensorなどのロボットが搭載している機能に対応したAPI（API名まんま）を呼び出して使えば良いだろう。([ROBOTIS' Robotis OP2: Reference](https://cyberbotics.com/doc/guide/robotis-op2?version=R2022a))<br>

## ROS2関連の調査記録

## Webots関連の調査記録
