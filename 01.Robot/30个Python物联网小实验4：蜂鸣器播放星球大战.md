* [30个物联网传感器小实验4：蜂鸣器播放星球大战](#30个物联网传感器小实验4蜂鸣器播放星球大战)
	* [无源蜂鸣器](#无源蜂鸣器)
	* [有源蜂鸣器：播放MIDI音乐](#有源蜂鸣器播放midi音乐)
	* [蜂鸣器播放星球大战](#蜂鸣器播放星球大战)
	* [激光传感器](#激光传感器)


## 30个物联网传感器小实验4：蜂鸣器播放星球大战

### 无源蜂鸣器

- 用途：

无源蜂鸣器用于释放报警音

- 代码：

```py
from gpiozero import Buzzer
import time
bz = Buzzer(2)

while True :
	bz.on()
	time.sleep(0.0005)
	bz.off()
	time.sleep(0.0005)
```

- 效果：

将蜂鸣器正极接`GPIO2`号口，负极接GND地线，运行上述代码，可以听到急促的报警音。

### 有源蜂鸣器：播放MIDI音乐

- 用途：

有源蜂鸣器可播放简单的音调，比如MIDI音乐，像我们小时候经常玩的生日贺卡，里面的生日快乐歌就是用有源蜂鸣器播放的。

- 代码：

```py
from gpiozero import TonalBuzzer
from gpiozero.tones import Tone
import time
b = TonalBuzzer(2)
i=220
while True:
	i=i+20
	b.play(i)
	time.sleep(1)
```

- 效果：

将蜂鸣器正极接`GPIO2`号口，负极接GND地线，运行上述代码，运行上述代码可以听到音调逐渐升高。

- 拓展：

当然，`TonalBuzzer`这个类可以播放音调的格式还有很多，比如下列音调的格式都是支持的：

```
>>> from gpiozero import TonalBuzzer
>>> from gpiozero.tones import Tone
>>> b = TonalBuzzer(17)
>>> b.play(Tone("A4"))
>>> b.play(Tone(220.0)) # Hz
>>> b.play(Tone(60)) # middle C in MIDI notation
>>> b.play("A4")
>>> b.play(220.0)
>>> b.play(60)
```

### 蜂鸣器播放星球大战

- 源码

在网上找到了星球大战主题曲的源码，不幸的是用`c`写的，不过也可以很轻松的看懂原理，我们来“品尝”一下。

```c
#include <wiringPi.h>
#include <stdio.h>

//Pin 11 on Raspberry Pi corresponds to BCM GPIO 17 and wiringPi pin 0 
//正极接GPIO17号口
#define BeepPin 8

//FREQUENCIES
//定义频率
#define cL 129
#define cLS 139
#define dL 146
#define dLS 156
#define eL 163
...
...
//This function generates the square wave that makes the piezo speaker sound at a determinated frequency.
//生成特定频率的声音
void beep(unsigned int note, unsigned int duration)
{
  //This is the semiperiod of each note.
  long beepDelay = (long)(1000000/note);
  //This is how much time we need to spend on the note.
  long time = (long)((duration*1000)/(beepDelay*2));
  for (int i=0;i<time;i++)
  {
    //1st semiperiod
    digitalWrite(BeepPin, HIGH);
    delayMicroseconds(beepDelay);
    //2nd semiperiod
    digitalWrite(BeepPin, LOW);
    delayMicroseconds(beepDelay);
  }

  //Add a little delay to separate the single notes
  digitalWrite(BeepPin, LOW);
  delay(20);
}

//The source code of the Imperial March from Star Wars
//星球大战主题曲音调鸣叫时长
void play()
{
  beep( a, 500);
  beep( a, 500);
  beep( f, 350);
  beep( cH, 150);
  ...
  ...

}

//开始播放
int main(void)
{
  //Check wiringPi setup
  if(-1 == wiringPiSetup())
  {
    printf("setup wiringPi failed!");
    return 1;
  }

  //Prepare GPIO0
  pinMode(BeepPin, OUTPUT);
  //Play the Imperial March
  play();

  return 0;
}
```

- 编译`c`源码

编译上述`c`源码需要预先安装好`wiringpi`这个库，这里推荐大家使用完整版的`Raspbian`，也就是官网上`Raspbian Stretch with desktop and recommended software`的版本，这样的话这个库已经是预置的。

编译的命令如下：

```
$ gcc starwars.c -o starwars -lwiringPi -std=c99
```

编译完成后，会生成一个`starwar`的可执行文件：

```
$ file starwars 
starwars: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, for GNU/Linux 3.2.0, BuildID[sha1]=8789b91feaa4e5da5f942f1833c8c765347abeee, not stripped
```

然后直接执行这个可执行文件即可：

```
$ ./starwars
```

即可听到星球大战的主题曲。

上述源码的完整版本请见：https://github.com/asukafighting/RaspberrypiHandbyHand/tree/master/01.Robot


### 激光传感器

最后再来介绍一个不相干的，激光传感器。
小时候也经常玩这个，用这个来逗猫或者逗狗。

![](pic/0117.gif)

点亮激光传感器的代码跟之前的LED是一模一样的，支持闪烁、淡入淡出等效果，在这里不做赘述。

好了，本次介绍就到这里，本文收录在我的《手把手教你玩树莓派》系列教程，立足于普及树莓派搭配**人工智能、物联网和机器人**的玩法，想要跟我一起学习的童鞋可以加我微信/微博ID：asukafighting，也可以直接看教程：https://github.com/asukafighting/RaspberrypiHandbyHand