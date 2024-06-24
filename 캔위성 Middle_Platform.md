## 캔위성 Middle_Platform

<img width="539" alt="Untitled (16)" src="https://github.com/dawoon1229/Cansat_project/assets/164113758/ae055ec7-de4f-4e16-a866-505819bac3c5">


이 회로도는 SD 카드 리더를 제어하는 데 사용되는 핀과 관련된 것이다.

### 주요 구성 요소:

1. MSDC M61408 : 이 모듈은 SD 카드 리더이다.
2. R26, R27, R28, R29, R30, R31, R32 : 이들은 저항기이다.
3. C14 : 10uF 커패시터.
4. PB4 : SD 카드 삽입 감지 핀.

### 핀 설명:

1. 핀 1 (DAT2) : 데이터 라인 2
2. 핀 2 (CD_DAT3) : 카드 감지 및 데이터 라인 3
3. 핀 3 (CMD) : 커맨드 라인
4. 핀 4 (VDD) : 전원 입력, 3.3V 연결
5. 핀 5 (CLK) : 클록 라인
6. 핀 6 (VSS) : 그라운드 (GND)
7. 핀 7 (DAT0) : 데이터 라인 0
8. 핀 8 (DAT1) : 데이터 라인 1
9. 핀 10 (card_detection) : 카드 감지 라인, PB4와 연결

### 3.3V 전압 공급:

1. 핀 4 (VDD) : MSDC M61408 모듈에 전원을 공급하기 위해 3.3V를 연결한다.
2. C14 커패시터 : 전원 공급의 안정성을 위해 3.3V와 GND 사이에 10uF 커패시터를 연결한다.

### 저항 네트워크 :

- R26, R27, R28 : 각각 2.2K 저항으로, 데이터 라인 2 (DAT0), 데이터 라인 3 (CD_DAT3), 커맨드 라인 (CMD)에 연결되어 있다.
- R29, R30, R31 : 각각 3.3K 저항으로, 데이터 라인 0 (DAT0), 데이터 라인 1 (DAT1), 카드 감지 라인 (card_detection)에 연결되어 있다.
- R32 : 10K 저항으로, PB4와 카드 감지 라인 (card_detection) 사이에 연결되어 있다.

### 결론:

- 3.3V 전압은 SD 카드 리더 모듈에 안정적인 전원을 공급한다.
- C14 커패시터는 전원 공급의 안정성을 높여준다.
- 저항기들은 데이터와 커맨드 라인의 신호를 안정적으로 유지하기 위해 사용된다.

따라서, MSDC M61408 모듈에 3.3V 전원을 제공하여 SD 카드 리더가 정상적으로 작동하도록 해야한다. 또한, 연결된 커패시터와 저항기들이 올바르게 배치되어 신호의 안정성을 유지하는지 확인해야 한다.

### SD 카드 리더부분 코드

```arduino
void setup() 
{
  hyCANSAT_INIT(BT_NEED_INIT);
  hyLed_PortSet() ;
  OP_FROM_EEPROM();
  // SD Card Init Setting //
  pinMode(SS,OUTPUT) ;    // SS pin
  if( SD.begin(SS))
  {
    hyCansat_Message("SD.begin OK\n" );
  }
  else
  {
    hyCansat_Message("SD.begin FAIL\n" );
  }
  csLogFile = SD.open("test.txt",FILE_WRITE);
 if( csLogFile ) 
 {
    hyCansat_Message("SD.open OK\n" );
 }
 else
 {
    hyCansat_Message("SD.open Fail\n" );
 }
 if( csLogFile ) csLogFile.println("testing 1.2.3");
 if( csLogFile ) csLogFile.close();
}
```

이 부분인데 SD.begin 과 [SD.open](http://SD.open) 이 자꾸 Fail이 떠서 회로도를 찾아보았다. 우선 키트가 올 때까지 2층 보드만 가지고 점검하고 있었는데, 결론적으로 2층 보드에서는 3.3V를 생성하는 곳이 없어서 SD카드 open이 되지 않았다. (SD카드는 3.3V로 동작하기 때문)

현재 코드부분에 SS의 값이 어떻게 설정되어있는지 분석중에 있다.

아두이노 함수 pinMode() 함수

```arduino
pinMode(pin, mode);
```

pinMode()는 특정한 핀을 입력으로 쓸지 출력으로 쓸지를 설정해주는 함수이다.

pin : 설정을 하려는 핀의 번호

mode : OUTPUT, INPUT 또는 INPUT_PULLUP

OUTPUT의 경우 출력 모드로 설정하게 되며, LED를 켜거나 모터를 구동하는 데 사용할 수 있다.

INPUT의 경우 입력 모드로 설정하게 되며, 센서값을 읽어오는 데 사용할 수 있다.

INPUT_PULLUP은 내부의 풀업저항을 사용하는 입력 모드이다.
