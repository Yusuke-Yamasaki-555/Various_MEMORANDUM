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
<br>
## ROS2関連の調査記録

## Webots関連の調査記録
