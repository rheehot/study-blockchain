## 머클 트리를 이용하여 거래 기록을 줄여도 정상 작동하는 이유

Full node와 Light node의 구분으로 가능.
풀 노드는 비트코인의 최초 블록 생성부터 현재의 최신 블록까지 모든 거래내역을 포함한 블록체인 노드
반면 라이트 노드는 각 블록에는 대부분의 경우 *블록 헤더* 만을 가지고 있을 뿐, 자신에게 연관된 일부 거래 내역만이 각 블록에 scarce하게 분포.
이 경우, 해당 거래 내역에 대한 검증이 필요할 때에만 해당 블록의 전체 거래 내역을 풀 노드로부터 다운로드받아서 해시값을 계산

----

## 새로운 거래 내역의 추가 vs 새로운 블록의 추가

블록은 거래 내역의 모음. 평균적으로 500개 정도가 들어있다고 함.
기존의 내역이 하나라도 변경되거나, 새로운 것이 추가되면 블록 헤더가 새로 계산되어야 함

1. 새로운 거래 내역이 블록에 추가되면, 해당 내역을 모든 노드에게 전달한다 (broadcast)
2. 각 노드들은 새로운 거래 내역을 각자 자신이 가진 블록 체인의 최신 블록에 추가하고,
   새로운 거래 내역이 추가되었으므로 작업 증명을 시작한다.
3. 그 결과 작업 증명을 성공한 최초의 노드가 등장하면, 해당 노드가 만든 새로운 블록을 다른 노드에게 전달한다 (broadcast)
4. 각 노드는 해당 블록에 추가된 새로운 거래 내역을 포함하여 거래 내역들이 모두 유효한지 (타임스탬프 검사) 검사하고, 유효한 경우에만 승인
   - [비트코인 백서 2. 거래 챕터 참조]
   - 각 거래 내역에는 이전 거래 내역이 포함되어 있다. 또한 새로운 소유자(수신자)의 공개키도 포함된다
   - 각 거래 내역에 포함된 것: [이전 거래 내역, 새로운 수신자(수신자)의 공개키](송신자에 의하여 서명된다) + 해당 거래 내역(수신자와 송신자를 알 수 있다)
   - ***(다시 채우기)***
5. 승인했다면, 해당 노드는 방금 승인한 블록을 *이전 해시* 로 삼아서 다음 블록을 생성하기 시작하는 것으로 해당 블록이 승인되었음을 인정한다.

----

## 작업 검증에서 공격자가 불리한 이유

가장 긴 길이의 블록체인을 인정하는 것이 원칙.
=> 이것을 깨는 것이 공격자가 하고자하는 바.
공격자가 성공하려면, 바꾸려는 거래 내역을 기준으로 그 뒤에 이어진 블록들을 모조리 바꿔야 함.
=> 블록마다 10분 정도가 걸리며, 공격자가 갱신하는 와중에서 새로운 블록이 또 추가되므로 사실상 불가능 (끝나지 않은 수정 작업...)
다수의 컴퓨팅 자원이 담합하여 위조된 블록체인을 동시다발로 만든다고 할지라도, 정상적인 경우 일반 노드들은 기존에 이미 형성된 가장 긴 블록체인을 기반으로 새 블록을 생성할 것이므로, 이러한 경향성을 깨고 51%의 다수가 되는 것은 사실상 어려움.



----

5.15.

서명이 있는 새로운 거래 발생
-> 발생과 동시에 전세계 각 노드에 거래가 전송됨
-> (동시다발적으로 수북히 쌓인 거래들을 이용하여) 블록을 만든다
-> 새로운 거래들을 사용하여 가장 빨리 작업 증명을 마친 노드가, 새 블록이 생겼다고 다른 노드에 전파
-> 새로운 블록을 받은 노드들은 자신이 작업하던 블록을 폐기하고, 새로 받은 블록을 블록 체인에 연결한 뒤, 또다시 새로운 블록 만들기 경쟁에 돌임

## 거래에 포함되어있는 것

- 해당 거래에 대한 내용 원본
- 위의 것을 전자 서명한 내용
=> 이 2개를 대조해서 무결성을 확인

- 이전 거래(single)에 대한 해시값 *(그런데, 머클 트리가 의미가 있나? 무조건 이전 거래가 포함되는 개념이라면... 구현은 다른건가?)*
- 비트코인 수신자의 공개키 (지갑 주소)
=> 비트코인 송신자의 개인키로 서명

##

거래가 블록체인 상에 기록되는 시간은 아무래도 좋다. 다만 거래시 직전 잔액이 유효하기만 하면 됨.
(어차피 블록체인은 장부이므로...)
하지만, 더블 스펜딩 문제는? => (해시 기반 Time-Stamp 원리로 인하여 항상)
- [트랙잭션 검증](https://steemit.com/kr/@yahweh87/15-3-n)
=> 시간 간격이 좀 있는 두 거래 내의 잔액이 서로 정당함을 어떻게 확인하지?
=> *어차피 잔액을 바꿀 수는 없고,* 모든 거래는 동시에 발생할 수 없음이 보장되므로, 해시 기반의 Time-stamp를 가지고 있기만 하다면 신뢰할 수 있다.

## 작업 증명의 대전제

- 모든 거래는 모든 노드에게 전파된다. (놓치는 거래는 존재할 수 없다)
  - 모든 노드는 자신이 가지고 있는 모든 거래"들"을 각자 독자적으로 검증한다
  - 모든 노드는 검증이 완료된 거래"들"를 이용하여 새로운 블록을 생성하고, 작업 증명을 시작한다
    - 얼마나 빠르게 컴퓨터가 연산해야하는지를 가늠해보라... 일반 피시로는 할 필요도 없어보인다.
  > - 채굴자가 자신이 만드려는 블록 안에, 보상으로 받을 새로운 비트코인이 “생성”되는 거래를 하나의 거래로써 포함시키는 것입니다. 일종의 셀프 보상인 셈이죠. 하지만 그냥 거래를 블록에 포함시켰을 뿐, 아직 승인된 것은 아닙니다. 채굴에 성공해야 받게 됩니다. 그리고 엄밀히 얘기하면 보상금만 있는 것은 아니고, 보상금과 함께 자신의 블록 안에 있는 모든 거래들, 즉 *10분동안 전 세계 각지에서 접수된 거래들의* 거래수수료까지 포함을 해서 합친 금액을 생성합니다.
  > - 채굴하는 동안 쌓이는 거래는 어떻게 보관하고 있을까요? 네트워크로 전송된 거래는 블록체인에 올라가기 전까지는 검증되지 않습니다. 10분마다 새 블록이 생성될 때, 비트코인 네트워크 노드들은 이 상황을 지켜보다가 각 노드가 유지하고 있는 임시 풀(temporary pool)에 새로운 거래들을 추가시켜놓습니다. 물론 여기에는 미검증 거래들이 들어있겠죠. 거래들이 새 블록에 추가될 때는 거래의 age나(발생한지 얼마나 오래됐는지), 거래 수수료가 가장 높은 거래부터 우선적으로 추가되어(다른 기준도 있지만) 새로운 블록에 대한 작업증명 계산을 시작합니다. 만약 한창 계산하고 있는데, 억세게 운 좋은 다른 채굴자가 1분 만에 솔루션을 찾아서, 그 채굴자으로부터 새로운 블록을 받게 되면, ‘이번 라운드에서는 내가 실패했구나’ 깨닫고 재빨리 새 블록을 채굴하는 과정을 다시 시작합니다.
  > - 조작이 의미없는게, 설령 조작을 해서 작업 증명에 성공했다 할지라도 그 블록은 또다시 한번 검증이 들어감. 알고리즘에 의하여 조작된 것은 바로 들통나게 되어있음. 핵손해.

- 조작 => 최초 블록부터 현재 거래까지의 해시를 모두 바꿔야 함. 시간이 지날수록 블록 체인이 길어지므로, 점점 더 어려워짐. 조작 자체가 핵손해가 됨.

- 블록 사이즈는 최대값이 정해져있다.
- 블록은 평균 10분 단위로 생성되도록 난이도가 저절로 조절된다
- 끊임없이 생성되는 내역을 기록할 블록이 유지되려면, 누군가는 블록을 계속 만들어줘야 한다.
  => 이 사람들을 위하여 사용료가 생겨나고, 블록을 최초로 만든 사람에게 주어지는 보상이 그것.



##

만약, 오랫동안 네트워크를 떠나 있다가 거래를 생성시켰다면,
자리를 비운 동안에 쌓인 블록들을 난 가지고 있지 않은데, 거래를 만들면 꼬이는건 아닌가?
=> 걱정 없다. 내가 거래를 만든 다음에 다른 사람에게 보내고, 내가 나만의 블록을 만들었다고 치더라도,
   *네트워크 상에서 가장 긴 블록체인만을 받아들이도록* 알고리즘이 되어있으므로, 내 outdated한 블록체인은 순식간에 갱신될 것이다.
=> 결국 항상 블록체인은 모든 노드에서 일관성을 유지하는 것

(http://www.leejungmin.org/post/2017/05/31/mastering-bitcoin/)
(https://steemit.com/@yahweh87)


===

##

똑같은 거래일지라도, 거래가 네트워크 상에서 다른 노드로 전파되는 속도에 물리적 한계가 있어서
어떤 노드보다 다른 노드가 거래를 늦게 전달받는 경우도 발생하므로
  - 높은 거래 수수료 경쟁에 의한 순위 밀림 등으로 인하여 밀리는 수도 있다
똑같은 거래가 어떤 블록에는 포함될 수도 있고, 어떤 블록에는 포함이 안 될 수도 있다.
  - 게다가 Stale Block의 발생으로 인하여, 앞쪽 블록에 위치했다가도 해당 블록이 폐기되어서 더 늦어버릴 수도 있다.
이에 따라 거래가 성립되는 시점은 최소 10분, 최대 1시간까지 걸릴 수도 있는 것이다.