# 7장. 스마트 컨트랙트와 솔리디티

2장에서 논의한 바와 같이, 이더리움에는 두 가지 다른 유형의 계정이 있다: EOA와 컨트랙트 계정. EOA는 사용자가 제어하며, 종종 이더리움 플랫폼 외부에 있는 지갑 애플리케이션과 같은 소프트웨어를 통해 제어된다. 반면 컨트랙트 계정은 EVM에서 실행되는 프로그램 코드(*스마트 컨트랙트*라고도 함)에 의해 제어된다.

간단히 말해, EOA는 연관된 코드나 데이터 저장소가 없는 단순한 계정이고, 컨트랙트 계정은 연관된 코드와 데이터 저장소를 모두 가지고 있다. EOA는 프로토콜과 독립적인 "실제 세계"에서 개인 키로 생성되고 암호화 서명된 트랜잭션에 의해 제어되는 반면, 컨트랙트 계정은 개인 키를 가지지 않으며 스마트 컨트랙트 코드에 의해 미리 정해진 방식으로 "스스로를 제어"한다. 두 유형의 계정 모두 이더리움 주소로 식별된다. 이 장에서는 컨트랙트 계정과 그것을 제어하는 프로그램 코드에 대해 논의할 것이다.

## 스마트 컨트랙트란 무엇인가?

*스마트 컨트랙트*라는 용어는 수년에 걸쳐 다양한 것들을 설명하는 데 사용되어 왔다. 1990년대에 암호학자 닉 사보는 이 용어를 만들고 "디지털 형태로 지정된 일련의 약속으로, 당사자들이 다른 약속을 이행하는 프로토콜을 포함한다"라고 정의했다. 그 이후로, 특히 2009년 비트코인 출시와 함께 탈중앙화 블록체인 플랫폼이 도입된 이후 스마트 컨트랙트의 개념은 발전해 왔다.

이더리움의 맥락에서, 이더리움 스마트 컨트랙트는 스마트하지도 않고 법적 계약도 아니기 때문에 이 용어는 사실 약간의 오해의 소지가 있지만, 용어는 그대로 사용되고 있다. 이 책에서 우리는 *스마트 컨트랙트*라는 용어를 이더리움 네트워크 프로토콜의 일부로서 EVM의 맥락에서 결정론적으로 실행되는 불변의 컴퓨터 프로그램, 즉 탈중앙화된 이더리움 월드 컴퓨터에서 실행되는 프로그램을 지칭하는 데 사용한다.

그 정의를 분해해 보자:

**컴퓨터 프로그램**

스마트 컨트랙트는 단순히 컴퓨터 프로그램이다. *컨트랙트*라는 단어는 이 맥락에서 법적 의미가 없다.

**불변성**

일단 배포되면 스마트 컨트랙트의 코드는 변경될 수 없다. 전통적인 소프트웨어와 달리, 스마트 컨트랙트를 수정하는 유일한 방법은 새로운 인스턴스를 배포하는 것이다.

**결정론적**

스마트 컨트랙트 실행의 결과는 그것을 실행하는 모든 사람에게 동일하며, 실행을 시작한 트랜잭션의 컨텍스트와 실행 시점의 이더리움 블록체인 상태가 주어진다.

**EVM 컨텍스트**

스마트 컨트랙트는 매우 제한된 실행 컨텍스트에서 작동한다. 자신의 상태, 자신을 호출한 트랜잭션의 컨텍스트, 그리고 가장 최근 블록에 대한 일부 정보에만 접근할 수 있다.

**탈중앙화 월드 컴퓨터**

EVM은 모든 이더리움 노드에서 로컬 인스턴스로 실행되지만, 모든 EVM 인스턴스가 동일한 초기 상태에서 작동하고 동일한 최종 상태를 생성하기 때문에, 시스템 전체는 단일 "월드 컴퓨터"로 작동한다.

## 스마트 컨트랙트의 생명 주기

스마트 컨트랙트는 일반적으로 솔리디티와 같은 고급 언어로 작성된다. 그러나 실행되려면 EVM에서 실행되는 저수준 바이트코드로 컴파일되어야 한다. 컴파일된 후에는 `to` 필드가 비어 있는 것이 특징인 특별한 컨트랙트 생성 트랜잭션을 사용하여 이더리움 플랫폼에 배포된다(6장의 "특별한 트랜잭션: 컨트랙트 생성" 참조). 각 컨트랙트는 원래 계정과 논스의 함수로서 컨트랙트 생성 트랜잭션에서 파생된 이더리움 주소로 식별된다. 컨트랙트의 이더리움 주소는 트랜잭션에서 수신자로 사용되어 컨트랙트에 자금을 보내거나 컨트랙트의 함수 중 하나를 호출할 수 있다. EOA와 달리, 새로운 스마트 컨트랙트를 위해 생성된 계정에는 연관된 키가 없다. 컨트랙트 생성자로서 프로토콜 수준에서 특별한 권한을 받지 않지만(스마트 컨트랙트에 명시적으로 코딩할 수 있음), 실제로 존재하지 않는 컨트랙트 계정의 개인 키를 받지 않는다—스마트 컨트랙트 계정은 스스로를 소유한다고 말할 수 있다.

중요한 것은, 컨트랙트는 *트랜잭션에 의해 호출될 때만 실행된다*. 이더리움의 모든 스마트 컨트랙트는 궁극적으로 EOA에서 시작된 트랜잭션 때문에 실행된다. 컨트랙트는 다른 컨트랙트를 호출할 수 있고, 그 컨트랙트는 또 다른 컨트랙트를 호출할 수 있지만, 이러한 실행 체인의 첫 번째 컨트랙트는 항상 EOA의 트랜잭션에 의해 호출되었을 것이다. 컨트랙트는 절대로 "자체적으로" 또는 "백그라운드에서" 실행되지 않는다. 컨트랙트는 직접 또는 컨트랙트 호출 체인의 일부로서 간접적으로 트랜잭션이 실행을 트리거할 때까지 사실상 휴면 상태에 있다. 스마트 컨트랙트가 어떤 의미에서도 "병렬로" 실행되지 않는다는 점도 주목할 가치가 있다—이더리움 월드 컴퓨터는 단일 스레드 머신으로 간주될 수 있다.

트랜잭션은 호출하는 컨트랙트 수나 호출 시 해당 컨트랙트가 수행하는 작업에 관계없이 *원자적*이다. 트랜잭션은 전체가 실행되며, 모든 실행이 성공적으로 종료될 때만 전역 상태(컨트랙트, 계정 등)의 변경 사항이 기록된다. *성공적인 종료*는 프로그램이 오류 없이 실행되어 실행 끝에 도달했음을 의미한다. 오류로 인해 실행이 실패하면 모든 효과(상태 변경)는 트랜잭션이 실행되지 않은 것처럼 "롤백"된다. 실패한 트랜잭션은 시도된 것으로 여전히 기록되고, 실행을 위해 가스에 지출된 이더는 원래 계정에서 차감되지만, 그 외에는 컨트랙트나 계정 상태에 영향을 미치지 않는다.

앞서 언급했듯이, 컨트랙트의 코드는 일단 배포되면 변경될 수 없다. 역사적으로, 컨트랙트는 삭제될 수 있었고, 그 코드와 내부 상태(저장소)를 주소에서 제거하여 빈 계정을 남겼다. 이러한 삭제 후에는 해당 주소로 전송된 모든 트랜잭션이 남아 있는 코드가 없기 때문에 코드 실행을 초래하지 않았다. 이 삭제는 `SELFDESTRUCT`라는 EVM 연산 코드를 사용하여 수행되었으며, 저장된 상태를 삭제하여 네트워크 리소스 해제를 장려하는 가스 환불을 제공했다. 그러나 `SELFDESTRUCT` 연산은 계정 상태, 특히 모든 코드와 저장소의 제거에 필요한 상당한 변경으로 인해 2023년 [EIP-6780](https://oreil.ly/5LcZo)에 의해 사용 중단되었다. 이더리움 로드맵의 향후 업그레이드로 이 연산은 더 이상 실행 가능하지 않게 될 것이다.

## 이더리움 고급 언어 소개

EVM은 x86_64와 같은 컴퓨터의 CPU가 머신 코드를 실행하는 것처럼, *EVM 바이트코드*라는 특수한 형태의 코드를 실행하는 가상 머신이다. 14장에서 EVM의 작동과 언어를 훨씬 더 자세히 살펴볼 것이다. 이 섹션에서는 스마트 컨트랙트가 EVM에서 실행되도록 작성되는 방법을 살펴볼 것이다.

바이트코드로 직접 스마트 컨트랙트를 프로그래밍하는 것이 가능하지만, EVM 바이트코드는 다루기 어렵고 프로그래머가 읽고 이해하기 매우 어렵다. 대신, 대부분의 이더리움 개발자는 고급 언어를 사용하여 프로그램을 작성하고 컴파일러를 사용하여 바이트코드로 변환한다.

모든 고급 언어가 스마트 컨트랙트를 작성하도록 적응될 수 있지만, 임의의 언어를 EVM 바이트코드로 컴파일 가능하도록 적응시키는 것은 상당히 번거로운 작업이며 일반적으로 어느 정도의 혼란을 야기할 것이다. 스마트 컨트랙트는 고도로 제약되고 최소한의 실행 환경(EVM)에서 작동한다. 또한, EVM 특정 시스템 변수와 함수의 특별한 집합이 사용 가능해야 한다. 따라서, 범용 언어를 스마트 컨트랙트 작성에 적합하게 만드는 것보다 처음부터 스마트 컨트랙트 언어를 구축하는 것이 더 쉽다. 결과적으로, 스마트 컨트랙트 프로그래밍을 위한 여러 특수 목적 언어가 등장했다. 이더리움에는 EVM 실행 가능한 바이트코드를 생성하는 데 필요한 컴파일러와 함께 여러 그러한 언어가 있다.

일반적으로, 프로그래밍 언어는 두 가지 광범위한 프로그래밍 패러다임으로 분류될 수 있다: *선언적*과 *명령적*, 각각 *함수형*과 *절차적*으로도 알려져 있다. 선언적 프로그래밍에서 우리는 프로그램의 *흐름*이 아닌 *로직*을 표현하는 함수를 작성한다. 선언적 프로그래밍은 *부작용*이 없는 프로그램을 만드는 데 사용되며, 이는 함수 외부의 상태에 변경이 없음을 의미한다. 선언적 프로그래밍 언어에는 Haskell과 SQL이 포함된다. 반면 명령적 프로그래밍은 프로그래머가 프로그램의 로직과 흐름을 결합하는 일련의 절차를 작성하는 것이다. 명령적 프로그래밍 언어에는 C++와 Java가 포함된다. 일부 언어는 "하이브리드"로, 선언적 프로그래밍을 권장하지만 명령적 프로그래밍 패러다임을 표현하는 데도 사용될 수 있다. 이러한 하이브리드에는 Lisp, JavaScript, Python이 포함된다. 일반적으로, 모든 명령적 언어는 선언적 패러다임으로 작성하는 데 사용될 수 있지만, 종종 우아하지 않은 코드를 초래한다. 비교하면, 순수 선언적 언어는 명령적 패러다임으로 작성하는 데 사용될 수 없다. 순수 선언적 언어에서는 "변수"가 없다.

명령적 프로그래밍이 프로그래머에 의해 더 일반적으로 사용되지만, 정확히 예상대로 실행되는 프로그램을 작성하는 것은 매우 어려울 수 있다. 프로그램의 어느 부분이든 다른 부분의 상태를 변경할 수 있는 능력은 프로그램의 실행에 대해 추론하기 어렵게 만들고 버그의 많은 기회를 도입한다. 비교하면, 선언적 프로그래밍은 부작용이 없기 때문에 프로그램이 어떻게 동작할지 이해하기 쉽게 만든다: 프로그램의 어느 부분이든 독립적으로 이해될 수 있다.

스마트 컨트랙트에서 버그는 말 그대로 돈이 든다. 결과적으로, 의도하지 않은 효과 없이 스마트 컨트랙트를 작성하는 것이 매우 중요하다. 그렇게 하려면 프로그램의 예상 동작에 대해 명확하게 추론할 수 있어야 한다. 따라서 선언적 언어는 범용 소프트웨어보다 스마트 컨트랙트에서 훨씬 더 큰 역할을 한다. 그럼에도 불구하고, 보시다시피, 스마트 컨트랙트에 가장 널리 사용되는 언어(솔리디티)는 명령적이다. 대부분의 인간과 마찬가지로 프로그래머도 변화에 저항한다!

현재 스마트 컨트랙트를 위해 지원되는 고급 프로그래밍 언어는 다음과 같다(인기도 순):

**솔리디티(Solidity)**

JavaScript, C++, 또는 Java와 유사한 구문을 가진 절차적(명령적) 프로그래밍 언어. 이더리움 스마트 컨트랙트에 가장 인기 있고 자주 사용되는 언어이다.

**율(Yul)**

솔리디티 내에서 독립 모드 또는 인라인으로 사용되는 중간 언어로, 플랫폼 전반에 걸쳐 고급 최적화에 이상적이다. 초보자는 Yul을 탐구하기 전에 솔리디티 또는 바이퍼로 시작해야 하는데, 스마트 컨트랙트 보안과 EVM에 대한 고급 지식이 필요하기 때문이다.

**바이퍼(Vyper)**

사용자 안전을 우선시하고 언어 설계와 효율적인 실행을 통해 명확한 코딩 관행을 장려하는 파이썬 같은 구문을 가진 컨트랙트 지향 프로그래밍 언어.

**허프(Huff)**

주로 솔리디티와 같은 고급 언어가 제공하는 것 이상의 고급 최적화를 허용하는, 고도로 효율적이고 최소한의 컨트랙트 코드가 필요한 개발자가 사용하는 저수준 프로그래밍 언어. Yul과 마찬가지로 초보자에게는 권장되지 않는다.

**페(Fe)**

Python과 Rust에서 영감을 받은 EVM을 위한 정적 타입 스마트 컨트랙트 언어. 2021년 1월 알파 릴리스 이후 개발이 아직 초기 단계에 있으며, 이더리움을 처음 접하는 개발자에게도 배우기 쉽게 설계되었다.

LLL, Serpent, Bamboo와 같은 다른 언어들은 과거에 개발되었지만 더 이상 유지되지 않는다.

보시다시피, 선택할 수 있는 많은 언어가 있다. 그러나 이 모든 것 중에서 솔리디티는 이더리움과 다른 EVM 유사 블록체인의 사실상 고급 언어가 될 정도로 단연코 가장 인기 있다.

## 솔리디티로 스마트 컨트랙트 구축하기

솔리디티는 이더리움 월드 컴퓨터의 탈중앙화 환경에서의 실행을 직접 지원하는 기능을 갖춘 스마트 컨트랙트 작성을 위해 명시적으로 언어로서 개빈 우드(이 책 초판의 공동 저자)가 만들었다. 그 결과 속성은 상당히 일반적이어서, 여러 다른 블록체인 플랫폼에서 스마트 컨트랙트를 코딩하는 데 사용되게 되었다. 크리스티안 라이트위스너가 개발했고 이후 알렉스 베레그자시, 리아나 후시키안, 요이치 히라이, 그리고 여러 전 이더리움 코어 기여자들이 개발했다. 솔리디티는 현재 [GitHub](https://oreil.ly/ik9kH)에서 독립 프로젝트로 개발되고 유지된다.

솔리디티 프로젝트의 주요 "제품"은 솔리디티 언어로 작성된 프로그램을 EVM 바이트코드로 변환하는 솔리디티 컴파일러 solc이다. 이 프로젝트는 또한 이더리움 스마트 컨트랙트를 위한 중요한 ABI 표준을 관리하며, 이 장에서 자세히 살펴볼 것이다. 솔리디티 컴파일러의 각 버전은 특정 버전의 솔리디티 언어에 해당하며 이를 컴파일한다.

시작하기 위해, 솔리디티 컴파일러의 바이너리 실행 파일을 다운로드할 것이다. 그런 다음, 2장에서 시작한 예제를 이어서 간단한 컨트랙트를 개발하고 컴파일할 것이다.

### 솔리디티 버전 선택하기

솔리디티는 [*시맨틱 버저닝*](https://semver.org)이라는 버전 관리 모델을 따르며, 세 개의 숫자가 점으로 구분된 버전 번호를 지정한다: MAJOR.MINOR.PATCH. "major" 숫자는 주요하고 하위 호환되지 않는 변경에 대해 증가하고, "minor" 숫자는 주요 릴리스 사이에 하위 호환 기능이 추가될 때 증가하며, "patch" 숫자는 하위 호환 버그 수정에 대해 증가한다.

이 글을 쓰는 시점에서, 솔리디티는 버전 0.8.26이다. 프로젝트의 초기 개발을 위한 주요 버전 0의 규칙은 다르다: 언제든지 무엇이든 변경될 수 있다. 실제로, 솔리디티는 "minor" 숫자를 major 버전처럼, "patch" 숫자를 minor 버전처럼 취급한다. 따라서, 0.8.26에서 8은 major 버전으로, 26은 minor 버전으로 간주된다. 2장에서 보았듯이, 솔리디티 프로그램은 호환되는 최소 및 최대 솔리디티 버전을 지정하고 컨트랙트를 컴파일하는 데 사용할 수 있는 pragma 지시문을 포함할 수 있다. 솔리디티가 빠르게 발전하고 있으므로, 최신 릴리스를 설치하는 것이 종종 더 좋다.

> **참고**
>
> 솔리디티의 빠른 발전의 또 다른 결과는 문서가 구식이 되는 속도이다. 지금 우리는 솔리디티 버전 0.8.26으로 작업하고 있으며, 이 책의 모든 것은 그 버전을 기반으로 한다. 이 책은 항상 솔리디티를 배우기 위한 견고한 기반을 제공하지만, 미래 버전은 일부 구문과 기능을 변경할 수 있다. 따라서, 질문이 있거나 새로운 것을 발견할 때마다, [공식 솔리디티 문서](https://oreil.ly/LzV7L)를 확인하여 최신 정보를 유지하는 것이 좋다.

### 솔리디티 다운로드 및 설치하기

운영 체제와 요구 사항에 따라 솔리디티를 다운로드하고 설치하는 데 사용할 수 있는 여러 방법이 있다: 바이너리 릴리스로 또는 소스 코드에서 컴파일하여. [솔리디티 문서](https://oreil.ly/JW--z)에서 자세하고 업데이트된 지침을 찾을 수 있다.

다음은 apt 패키지 관리자를 사용하여 Ubuntu/Debian 운영 체제에 솔리디티의 최신 바이너리 릴리스를 설치하는 방법이다:

```bash
$ sudo add-apt-repository ppa:ethereum/ethereum
$ sudo apt update
$ sudo apt install solc
```

solc가 설치되면, 다음을 실행하여 버전을 확인한다:

```bash
$ solc --version
solc, the solidity compiler commandline interface
Version: 0.8.26+commit.8a97fa7a.Linux.g++
```

### 개발 환경

간단한 텍스트 편집기를 사용하여 솔리디티 스마트 컨트랙트를 개발하는 것이 완전히 가능하지만, [Hardhat](https://hardhat.org)이나 [Foundry](https://oreil.ly/-7Qvy)와 같은 개발 프레임워크를 활용하면 개발자로서의 효율성과 효과를 크게 높일 수 있다. 이러한 프레임워크는 개발 프로세스를 단순화하고 개선하는 포괄적인 도구 모음을 제공한다. 예를 들어, 컨트랙트의 동작을 검증하기 위한 단위 테스트를 작성하고 실행할 수 있는 강력한 테스트 환경을 제공하고, 현실적인 테스트 시나리오를 위해 메인넷의 로컬 인스턴스를 생성하는 포킹 기능을 제공한다. 고급 디버깅 및 추적 기능으로 코드 실행을 쉽게 단계별로 진행하고 문제를 빠르게 식별하고 해결하여 시간을 절약하고 오류를 줄일 수 있다. 또한, 이러한 프레임워크는 스크립팅 및 배포 자동화, 기능을 확장하는 플러그인 에코시스템, 다양한 환경에 걸친 원활한 네트워크 관리를 지원한다. 이러한 기능을 워크플로에 통합하면 간단한 텍스트 편집기로는 달성하기 어려운 높은 수준의 코드 품질과 보안이 보장된다.

프레임워크 외에도, VS Code와 같은 최신 IDE를 채택하면 생산성이 더욱 향상된다. VS Code는 코드를 더 쉽게 읽을 수 있게 만드는 구문 강조, 복잡한 프로젝트를 구성하고 탐색하는 데 도움이 되는 고급 주석 달기 및 북마킹 도구, 코드 구조와 잠재적 문제에 대한 통찰력을 제공하는 시각적 분석 도구를 포함하여 솔리디티를 위한 다양한 확장 기능을 제공한다. [Remix IDE](https://oreil.ly/Fz0jJ)와 같은 웹 기반 개발 환경도 있다.

이러한 도구들은 코드 품질을 향상시킬 뿐만 아니라 개발 프로세스를 가속화하여 스마트 컨트랙트를 더 빠르고 안전하게 구축하고 배포할 수 있게 해준다.

### 간단한 솔리디티 프로그램 작성하기

2장에서 우리는 첫 번째 솔리디티 프로그램을 작성했다. `Faucet` 컨트랙트를 처음 구축할 때, Remix IDE를 사용하여 컨트랙트를 컴파일하고 배포했다. 이 섹션에서는 `Faucet`을 다시 살펴보고, 개선하고, 꾸밀 것이다.

우리의 첫 번째 시도는 예제 7-1과 같았다.

**예제 7-1. Faucet.sol: faucet을 구현하는 솔리디티 컨트랙트**

```solidity
// SPDX-License-Identifier: GPL-3.0
// Our first contract is a faucet!
contract Faucet {
    // Give out ether to anyone who asks
    function withdraw(uint _withdrawAmount, address payable _to) public {
        // Limit withdrawal amount
        require(_withdrawAmount <= 100000000000000000);
        // Send the amount to the address that requested it
        _to.transfer(_withdrawAmount);
    }
    // Accept any incoming amount
    receive() external payable {}
}
```

2장에서 보았듯이, 주석의 SPDX 라이선스 식별자는 스마트 컨트랙트가 GPL-3.0에 따라 라이선스되었음을 나타내며, 코드를 사용하고 배포하기 위한 법적 권리와 의무에 대해 사용자와 개발자에게 알려준다.

### 솔리디티 컴파일러(solc)로 컴파일하기

이제, 명령줄에서 솔리디티 컴파일러를 사용하여 컨트랙트를 직접 컴파일할 것이다. 솔리디티 컴파일러 solc는 다양한 옵션을 제공하며, `--help` 인수를 전달하여 볼 수 있다.

예제 컨트랙트의 최적화된 바이너리를 생성하기 위해 solc의 `--bin` 및 `--optimize` 인수를 사용한다:

```bash
$ solc --optimize --bin Faucet.sol
======= Faucet.sol:Faucet =======
Binary:
6080604052348015600e575f5ffd5b5060fa8061001b5f395ff3fe608060405260043610601d575f3560e01c806
2f714ce146027575f5ffd5b36602357005b5f5ffd5b3480156031575f5ffd5b506041603d366004608d565b6043
565b005b67016345785d8a00008211156056575f5ffd5b6040516001600160a01b0382169083156108fc0290849
05f818181858888f193505050501580156088573d5f5f3e3d5ffd5b505050565b5f5f60408385031215609d575f
5ffd5b8235915060208301356001600160a01b038116811460b9575f5ffd5b80915050925092905056fea264697
06673582212208935b6cf5d9070b7609ad59ac4b727e512522c674cacf09a2eff88dafa3242ee64736f6c634300
081b0033
```

solc가 생성하는 결과는 이더리움 블록체인에 제출할 수 있는 16진수 직렬화 바이너리이다.

## 이더리움 컨트랙트 ABI

컴퓨터 소프트웨어에서, *애플리케이션 바이너리 인터페이스*는 두 프로그램 모듈 간의 인터페이스로—종종 운영 체제와 사용자 프로그램 간의 인터페이스이다. ABI는 데이터 구조와 함수가 *머신 코드*에서 어떻게 접근되는지 정의한다; 이것은 고수준, 종종 사람이 읽을 수 있는 형식으로 *소스 코드*로 이 접근을 정의하는 API와 혼동해서는 안 된다. 따라서 ABI는 데이터를 머신 코드로 인코딩하고 디코딩하는 주요 방법이다.

이더리움에서, ABI는 EVM을 위한 컨트랙트 호출을 인코딩하고 트랜잭션에서 데이터를 읽는 데 사용된다. ABI의 목적은 호출될 수 있는 컨트랙트의 함수를 정의하고 각 함수가 인수를 어떻게 받아들이고 결과를 반환할지 설명하는 것이다.

컨트랙트의 ABI는 함수 설명("함수" 참조)과 이벤트("이벤트" 참조)의 JSON 배열로 지정된다. 함수 설명은 `type`, `name`, `inputs`, `outputs`, `constant`, `payable` 필드를 가진 JSON 객체이다. 이벤트 설명 객체는 `type`, `name`, `inputs`, `anonymous` 필드를 가진다.

*Faucet.sol* 예제 컨트랙트에 대한 ABI를 생성하기 위해 solc 명령줄 솔리디티 컴파일러를 사용한다:

```bash
$ solc --abi Faucet.sol
======= Faucet.sol:Faucet =======
Contract JSON ABI
[{"inputs":[{"internalType":"uint256","name":"withdrawAmount","type":"uint256"}],
"name":"withdraw","outputs":[],"stateMutability":"nonpayable","type":"function"},
{"stateMutability":"payable","type":"receive"}]
```

보시다시피, 컴파일러는 *Faucet.sol*에 의해 정의된 두 함수를 설명하는 JSON 배열을 생성한다. 이 JSON은 배포된 후 `Faucet` 컨트랙트에 접근하려는 모든 애플리케이션에서 사용될 수 있다. ABI를 사용하면, 지갑이나 DApp 브라우저와 같은 애플리케이션이 올바른 인수와 인수 타입으로 `Faucet`의 함수를 호출하는 트랜잭션을 구성할 수 있다. 예를 들어, 지갑은 `withdraw` 함수를 호출하려면 `withdrawAmount`라는 `uint256` 인수를 제공해야 한다는 것을 알 것이다. 지갑은 사용자에게 그 값을 제공하도록 요청한 다음, 그것을 인코딩하고 `withdraw` 함수를 실행하는 트랜잭션을 생성할 수 있다.

애플리케이션이 컨트랙트와 상호 작용하는 데 필요한 것은 ABI와 컨트랙트가 배포된 주소뿐이다.

## 솔리디티 컴파일러 및 언어 버전 선택하기

이전 코드에서 보았듯이, 우리의 `Faucet` 컨트랙트는 솔리디티 버전 0.8.26으로 성공적으로 컴파일된다. 하지만 다른 버전의 솔리디티 컴파일러를 사용했다면 어떨까? 언어는 여전히 끊임없이 변화하고 있으며, 예상치 못한 방식으로 변경될 수 있다. 우리의 컨트랙트는 상당히 간단하지만, 솔리디티 버전 0.8.26에서만 추가된 기능을 프로그램이 사용했고 0.8.25로 컴파일하려고 했다면 어떨까?

이러한 문제를 해결하기 위해, 솔리디티는 프로그램이 특정 컴파일러(및 언어) 버전을 기대한다는 것을 컴파일러에 지시하는 *버전 pragma*라는 컴파일러 지시문을 제공한다. 예제를 살펴보자:

```solidity
pragma solidity 0.8.26;
```

솔리디티 컴파일러는 버전 pragma를 읽고 컴파일러 버전이 버전 pragma와 호환되지 않으면 오류를 생성한다. 이 경우, 우리의 버전 pragma는 이 프로그램이 버전 0.8.26을 가진 솔리디티 컴파일러로 컴파일될 수 있다고 말한다. Pragma 지시문은 EVM 바이트코드로 컴파일되지 않는다; 호환성을 확인하기 위해 컴파일러만 사용하는 컴파일 타임 지시문이다.

> **참고**
>
> pragma 지시문에서, ^ 기호는 지정된 것과 동일하거나 그 이상인 모든 *마이너 리비전*으로 컴파일을 허용한다고 명시한다. 예를 들어, pragma 지시문 `pragma solidity ^0.7.1;`은 컨트랙트가 solc 버전 0.7.1, 0.7.2, 0.7.3으로 컴파일될 수 있지만 0.8.0(마이너 리비전이 아닌 메이저 리비전)으로는 컴파일될 수 없음을 의미한다.

`Faucet` 컨트랙트에 pragma 지시문을 추가하자. 이러한 예제를 진행하면서 변경 사항을 추적하기 위해 새 파일의 이름을 *Faucet2.sol*로 지정할 것이다. 예제 7-2에서 시작한다.

**예제 7-2. Faucet2.sol: Faucet에 버전 pragma 추가**

```solidity
pragma solidity 0.8.26;
// SPDX-License-Identifier: GPL-3.0
// Our first contract is a faucet!
contract Faucet {
    // Give out ether to anyone who asks
    function withdraw(uint _withdrawAmount, address payable _to) public {
        // Limit withdrawal amount
        require(_withdrawAmount <= 100000000000000000);
        // Send the amount to the address that requested it
        _to.transfer(_withdrawAmount);
    }
    // Accept any incoming amount
    receive() external payable {}
}
```

버전 pragma를 추가하는 것은 불일치하는 컴파일러와 언어 버전 문제를 피하기 때문에 모범 사례이다. 이 장 전체에서 다른 모범 사례를 탐구하고 `Faucet` 컨트랙트를 계속 개선할 것이다.

## 솔리디티로 프로그래밍하기

이 섹션에서는 솔리디티 언어의 일부 기능을 살펴볼 것이다. 2장에서 언급했듯이, 우리의 첫 번째 컨트랙트 예제는 매우 간단했고 다양한 방면에서 결함이 있었다. 여기서 점진적으로 개선할 것이다. 그러나 이것은 솔리디티가 상당히 복잡하고 빠르게 발전하고 있기 때문에 포괄적인 솔리디티 튜토리얼이 아닐 것이다. 기본 사항을 다루고 스스로 나머지를 탐구할 수 있는 충분한 기반을 제공할 것이다.

### 데이터 타입

먼저, 솔리디티에서 제공하는 일부 기본 데이터 타입을 살펴보자:

**불리언(Boolean, bool)**

불리언 값, `true` 또는 `false`, 논리 연산자 ! (not), && (and), || (or), == (equal), != (not equal)을 사용한다.

**정수(Integer, int, uint)**

8비트 단위로 `int8`에서 `uint256`까지 선언되는 부호 있는(`int`) 및 부호 없는(`uint`) 정수. 크기 접미사 없이, EVM의 워드 크기와 일치하도록 256비트 수량이 사용된다.

**고정 소수점(Fixed point, fixed, ufixed)**

*M*은 비트 단위 크기(8씩 증가하여 256까지)이고 *N*은 소수점 이하 자릿수(최대 18)인 `(u)fixedMxN`으로 선언되는 고정 소수점 수—예: `ufixed32x2`.

> **참고**
>
> 고정 소수점 수는 아직 솔리디티에서 완전히 지원되지 않는다. 선언될 수 있지만 할당하거나 할당받을 수 없다.

**주소(Address)**

20바이트 이더리움 주소. `address` 객체는 많은 유용한 멤버 함수를 가지며, 주요한 것은 `balance`(계정 잔액 반환)와 `transfer`(계정에 이더 전송)이다.

**바이트 배열(고정)(Byte array, fixed)**

`bytes1`에서 `bytes32`까지 선언되는 고정 크기 바이트 배열.

**바이트 배열(동적)(Byte array, dynamic)**

`bytes` 또는 `string`으로 선언되는 가변 크기 바이트 배열.

**열거형(Enum)**

이산 값을 열거하기 위한 사용자 정의 타입: `enum NAME {LABEL1, LABEL 2, ...}`. 열거형의 기본 타입은 `uint8`이다; 따라서, 256개 이하의 멤버를 가질 수 있으며 모든 정수 타입으로 명시적으로 변환될 수 있다.

**배열(Arrays)**

고정 또는 동적인 모든 타입의 배열: `uint32[][5]`는 부호 없는 정수의 다섯 개 동적 배열의 고정 크기 배열이다.

**구조체(Struct)**

변수를 그룹화하기 위한 사용자 정의 데이터 컨테이너: `struct NAME {TYPE1 VARIABLE1; TYPE2 VARIABLE2; ...}`.

**매핑(Mapping)**

*key* ⇒ *value* 쌍을 위한 해시 조회 테이블: `mapping(KEY_TYPE` `⇒` `VALUE_TYPE) NAME`.

이러한 데이터 타입 외에도, 솔리디티는 다양한 단위를 계산하는 데 사용할 수 있는 다양한 값 리터럴을 제공한다:

**시간 단위**

전역 변수 `block.timestamp`는 블록이 게시되어 블록체인에 추가된 시간을 Unix Epoch(1970년 1월 1일)부터 초 단위로 나타낸다. `seconds`, `minutes`, `hours`, `days` 단위는 기본 단위 `seconds`의 배수로 변환하는 접미사로 사용될 수 있다.

> **참고**
>
> 블록은 여러 트랜잭션을 포함할 수 있으므로, 블록 내의 모든 트랜잭션은 각 트랜잭션이 시작된 정확한 순간이 아니라 블록이 게시된 시간을 반영하는 동일한 `block.timestamp`를 공유한다.

**이더 단위**

`wei`와 `ether` 단위는 기본 단위 `wei`의 배수로 변환하는 접미사로 사용될 수 있다. 이전에는 `finney`와 `szabo` 명칭도 사용 가능했지만, 솔리디티 버전 0.7.0에서 삭제되었다.

`Faucet` 컨트랙트 예제에서, 우리는 `withdrawAmount` 변수에 `uint`(`uint256`의 별칭)를 사용했다. 또한 `msg.sender`로 설정한 `address` 변수를 간접적으로 사용했다. 이 장의 나머지 예제에서 이러한 데이터 타입을 더 많이 사용할 것이다.

예제 컨트랙트의 가독성을 높이기 위해 단위 승수 중 하나를 사용해 보자. `withdraw` 함수에서 우리는 최대 인출액을 제한하며, 이더의 기본 단위인 wei로 한도를 표현한다:

```solidity
require(withdrawAmount <= 100000000000000000);
```

이것은 읽기가 쉽지 않다. wei 대신 이더 단위로 값을 표현하기 위해 단위 승수 `ether`를 사용하여 코드를 개선할 수 있다:

```solidity
require(withdrawAmount <= 0.1 ether);
```

### 변수: 정의 및 범위

솔리디티에서, 변수와 함수를 정의하는 구문은 다른 정적 타입 언어와 유사하다: 각 변수에 타입, 이름, 그리고 선택적 값을 할당한다. 상태 변수의 경우, 가시성도 지정할 수 있다. 기본 가시성은 `internal`로, 변수가 컨트랙트와 그 파생 컨트랙트 내에서만 접근 가능함을 의미한다. 다른 스마트 컨트랙트에서 접근 가능하게 하려면 `public` 가시성을 사용해야 한다.

솔리디티 스마트 컨트랙트는 세 가지 유형의 변수 범위를 특징으로 한다:

**상태 변수**

블록체인에 값을 기록하여 스마트 컨트랙트에 *영구 상태*로 알려진 영구적인 데이터를 저장한다. 상태 변수는 스마트 컨트랙트 내에서 정의되지만 모든 함수 외부에 있다. 예: `uint public count;`

**로컬 변수**

짧은 기간 동안 정보를 보유하는 계산 중에 사용되는 임시 데이터 조각이다. 로컬 변수는 블록체인에 저장되지 않는다; 함수 내에 존재하며 정의된 범위 외부에서 접근할 수 없다. 예: `uint count = 1;`

**전역 변수**

솔리디티에서 자동으로 제공되며 명시적 선언이나 가져오기 없이 사용할 수 있다. 블록체인 환경에 대한 정보를 제공하고 프로그램 내에서 사용할 유틸리티 함수를 포함한다. 미리 정의된 전역 변수는 다음 섹션에서 완전히 나열된다.

간단히 언급했듯이, 상태 변수는 가시성을 지정하여 선언될 수 있다. 솔리디티는 세 가지 다른 가시성 수준을 제공한다: *public 변수*는 자동 getter 함수를 생성하여 외부 컨트랙트가 그 값을 읽을 수 있게 하지만 수정할 수는 없다; *internal 변수*는 컨트랙트와 그 파생 컨트랙트 내에서만 접근 가능하다; *private 변수*는 internal 변수와 유사하지만 파생 컨트랙트에서도 접근할 수 없다.

### 미리 정의된 전역 변수와 함수

컨트랙트가 EVM에서 실행될 때, 작은 전역 객체 집합에 접근할 수 있다. 여기에는 `block`, `msg`, `tx` 객체가 포함된다. 또한, 솔리디티는 여러 EVM 연산 코드를 미리 정의된 함수로 노출한다. 이 섹션에서는 솔리디티의 스마트 컨트랙트 내에서 접근할 수 있는 변수와 함수를 살펴볼 것이다.

#### 트랜잭션/메시지 호출 컨텍스트

`msg` 객체는 이 컨트랙트 실행을 시작한 트랜잭션 호출(EOA 발원) 또는 메시지 호출(컨트랙트 발원)이다. 여러 유용한 속성을 포함한다:

**msg.sender**

우리는 이미 이것을 사용했다. 트랜잭션을 보낸 원래 EOA가 아닌, 이 컨트랙트 호출을 시작한 주소를 나타낸다. 우리 컨트랙트가 EOA 트랜잭션에 의해 직접 호출되었다면 이것은 트랜잭션에 서명한 주소이지만, 그렇지 않으면 컨트랙트 주소가 될 것이다.

**msg.value**

이 호출과 함께 전송된 이더의 값(wei 단위).

**msg.data**

우리 컨트랙트에 대한 이 호출의 데이터 페이로드.

**msg.sig**

데이터 페이로드의 처음 4바이트로, 함수 셀렉터이다.

> **참고**
>
> 컨트랙트가 다른 컨트랙트를 호출할 때마다, `msg`의 모든 속성 값이 새 호출자의 정보를 반영하도록 변경된다. 이에 대한 유일한 예외는 원래 `msg` 컨텍스트 내에서 다른 컨트랙트나 라이브러리의 코드를 실행하는 `delegatecall` 함수이다.

#### 트랜잭션 컨텍스트

`tx` 객체는 트랜잭션 관련 정보에 접근하는 수단을 제공한다:

**tx.gasprice**

호출 트랜잭션의 가스 가격

**tx.origin**

이 트랜잭션의 원래 EOA 주소

#### 블록 컨텍스트

`block` 객체는 현재 블록에 대한 다음 정보를 포함한다:

**block.basefee**

현재 블록의 기본 수수료로, 블록에 트랜잭션이 포함되기 위해 필요한 최소 가스 수수료를 나타내는 동적으로 조정되는 값이다.

**block.blobbasefee**

EIP-4844를 통한 이더리움의 확장성 개선의 일부로 대용량 데이터를 효율적으로 처리하기 위해 도입된 blob 트랜잭션에 대한 동적으로 조정되는 기본 수수료이다.

**block.chainid**

블록이 현재 구축되고 있는 블록체인의 고유 식별자.

**block.prevrandao**

비콘 체인에서 제공하는 이전 블록의 무작위성 비콘에서 파생된 의사 난수 값. 난수가 필요한 스마트 컨트랙트에 유용할 수 있지만—어느 정도 조작될 수 있으므로 민감하지 않은 작업에만 사용해야 한다.

**block.coinbase**

현재 블록의 수수료와 블록 보상을 받는 수신자의 주소.

**block.difficulty**

Paris(The Merge) 이전 EVM 버전의 현재 블록 난이도(PoW). PoS 합의 모델을 채택하는 후속 EVM 버전의 경우, `block.prevrandao`의 더 이상 사용되지 않는 별칭으로 동작한다.

**block.gaslimit**

현재 블록에 포함된 모든 트랜잭션에서 사용할 수 있는 최대 가스량.

**block.number**

현재 블록 번호(블록체인 높이).

**block.timestamp**

채굴자가 현재 블록에 넣은 타임스탬프(Unix epoch 이후의 초 수).

#### 주소 객체

입력으로 전달되거나 컨트랙트 객체에서 캐스팅된 모든 주소는 여러 속성과 메서드를 가진다.

**address.balance**

wei 단위의 주소 잔액. 예를 들어, 현재 컨트랙트 잔액은 `address(this).balance`이다.

**address.code**

주소에 저장된 컨트랙트 바이트코드. EOA 주소의 경우 빈 바이트 배열을 반환한다.

**address.codehash**

주소에 저장된 컨트랙트 바이트코드의 Keccak-256 해시.

**address.transfer(amount)**

금액(wei 단위)을 이 주소로 전송하며, 오류 발생 시 예외를 throw한다. `Faucet` 예제에서 이 함수를 `msg.sender` 주소의 메서드로 `msg.sender.transfer`처럼 사용했다.

**address.send(amount)**

`transfer`와 유사하지만, 예외를 throw하는 대신 오류 시 `false`를 반환한다. `send`의 반환 값을 항상 확인하도록 주의해야 한다.

**address.call(payload)**

데이터 페이로드로 임의의 메시지 호출을 구성할 수 있는 저수준 `CALL` 함수. 오류 시 `false`를 반환한다. 주의: 수신자가 (실수로 또는 악의적으로) 모든 가스를 사용하여 컨트랙트가 OOG(out of gas) 예외로 중단될 수 있다; 항상 `call`의 반환 값을 확인해야 한다.

**address.delegatecall(payload)**

`address(this).call(...)`과 같은 저수준 `DELEGATECALL` 함수이지만, 이 컨트랙트의 코드가 `address`의 코드로 대체된다. 프록시 패턴 구현에 특히 유용하다. 오류 시 `false`를 반환한다. 경고: 고급 사용만!

**address.staticcall(payload)**

`address(this).call(...)`과 같은 저수준 `STATICCALL` 함수이지만 읽기 전용 모드로, 호출된 함수가 상태를 수정하거나 이더를 보낼 수 없다. 오류 시 `false`를 반환한다.

> **참고**
>
> `address.send()`와 `address.transfer()` 모두 고정된 2,300 단위의 가스를 전달하는데, 이는 fallback 로직을 실행하기에 충분하지 않을 수 있다. EIP-7702가 적용됨에 따라, 더 유연한 `address.call()`을 선호하여 사용이 권장되지 않는다. 이에 대한 자세한 내용은 9장에서 다룬다.

#### 내장 함수

주목할 만한 다른 함수들:

**addmod, mulmod**

모듈로 덧셈과 곱셈. 예를 들어, `addmod(x,y,k)`는 (x + y) % k를 계산한다.

**keccak256, sha256, ripemd160**

다양한 표준 해시 알고리즘으로 해시를 계산하는 함수.

**ecrecover**

서명에서 메시지에 서명하는 데 사용된 주소를 복구한다.

**selfdestruct(recipient_address)**

더 이상 사용되지 않음. 현재 컨트랙트를 삭제하고 계정에 남아 있는 이더를 수신자 주소로 보내는 데 사용되었다. EIP-6780 이후, 이는 self-destruct 명령이 생성과 동일한 트랜잭션에서 호출될 때만 발생한다. 다른 모든 경우에는 자금이 이동되지만 컨트랙트와 그 상태는 지워지지 않는다.

**this**

현재 컨트랙트로, 현재 실행 중인 컨트랙트 계정의 주소를 검색하기 위해 `Address` 타입으로 명시적으로 변환 가능하다: `address(this)`.

**super**

상속 계층에서 한 수준 위에 있는 컨트랙트.

**gasleft**

현재 실행 컨텍스트에 남아 있는 가스량.

**blockhash(block_number)**

블록 번호로 식별되는 주어진 블록의 해시; 가장 최근 256개 블록에 대해서만 사용 가능하다.

**blobhash(index)**

현재 트랜잭션과 연관된 `index`번째 blob의 해시.

### 컨트랙트 정의

솔리디티의 주요 데이터 타입은 `contract`이다; 우리의 `Faucet` 예제는 단순히 `contract` 객체를 정의한다. 객체 지향 언어의 모든 객체와 유사하게, *contract*는 데이터와 메서드를 포함하는 컨테이너이다.

솔리디티는 컨트랙트와 유사한 두 가지 다른 객체 타입을 제공한다:

**인터페이스(interface)**

인터페이스 정의는 컨트랙트와 정확히 동일하게 구조화되어 있지만, 함수가 정의되지 않고—선언만 된다. 이러한 유형의 선언은 종종 *스텁(stub)*이라고 불린다; 구현 없이 함수의 인수와 반환 타입을 알려준다. 인터페이스는 컨트랙트의 "형태"를 지정한다; 상속될 때, 인터페이스에 의해 선언된 각 함수는 자식에 의해 정의되어야 한다.

**라이브러리(library)**

라이브러리 컨트랙트는 한 번만 배포되고 `delegatecall` 메서드를 사용하여 다른 컨트랙트에서 사용되도록 의도된 것이다("주소 객체" 참조).

### 함수

컨트랙트 내에서 EOA 트랜잭션이나 다른 컨트랙트에 의해 호출될 수 있는 함수를 정의한다. `Faucet` 예제에서는 `withdraw`와 `receive` 두 개의 함수가 있다.

솔리디티에서 함수를 선언하는 데 사용하는 구문은 다음과 같다:

```solidity
function FunctionName([parameters]) {public|private|internal|external} [virtual|override]
[pure|view|payable] [modifiers] [returns (return types)]
```

각 구성 요소를 살펴보자:

**FunctionName**

함수의 이름으로, EOA에서의 트랜잭션, 다른 컨트랙트, 또는 동일한 컨트랙트 내에서 함수를 호출하는 데 사용된다.

**parameters**

이름 다음에, 함수에 전달해야 하는 인수를 이름과 타입으로 지정한다. `Faucet` 예제에서는 `withdraw` 함수의 유일한 인수로 `uint withdrawAmount`를 정의했다.

다음 키워드 집합(`public`, `private`, `internal`, `external`)은 함수의 가시성을 지정한다:

**public**

Public이 기본값이다; 이러한 함수는 다른 컨트랙트나 EOA 트랜잭션 또는 컨트랙트 내에서 호출될 수 있다. `Faucet` 예제에서 두 함수 모두 public으로 정의되었다.

**private**

Private 함수는 internal 함수와 같지만 파생 컨트랙트에서 호출될 수 없다.

**internal**

Internal 함수는 컨트랙트 내에서만 접근 가능하다—다른 컨트랙트나 EOA 트랜잭션에서 호출될 수 없다. 파생 컨트랙트(이 컨트랙트를 상속하는 컨트랙트)에서 호출될 수 있다.

**external**

External 함수는 public 함수와 같지만 `this` 키워드로 명시적으로 접두사를 붙이지 않는 한 컨트랙트 내에서 호출될 수 없다.

*internal*과 *private*이라는 용어가 다소 오해의 소지가 있다는 점을 명심해야 한다. 컨트랙트 내부의 모든 함수나 데이터는 공개 블록체인에서 항상 *보인다*, 이는 누구나 코드나 데이터를 볼 수 있음을 의미한다. 여기서 설명한 키워드는 함수가 언제 어떻게 *호출*될 수 있는지에만 영향을 미친다.

> **참고**
>
> 함수 가시성을 상태 변수 가시성과 혼동해서는 안 된다! 키워드와 의미를 공유하지만, 두 가지 다른 것이다. 상태 변수 가시성은 선택 사항이지만, 함수 가시성은 명시적으로 정의되어야 한다.

두 번째 키워드 집합(`pure`, `view`, `payable`)은 함수의 동작에 영향을 미친다:

**pure**

pure 함수는 저장소의 어떤 변수도 읽거나 쓰지 않는 함수이다. 저장된 데이터나 블록체인 상태에 대한 참조 없이 인수에서만 작동하고 데이터를 반환할 수 있다. pure 함수는 부작용이나 상태 없이 선언적 스타일 프로그래밍을 장려하기 위한 것이다.

**view**

view로 표시된 함수는 상태를 수정하지 않을 것을 약속한다. 컴파일러는 `view` 수정자를 강제하지 않는다; 적용될 수 있을 때만 경고를 생성한다.

**payable**

payable 함수는 들어오는 지불을 받을 수 있는 함수이다. `payable`로 선언되지 않은 함수는 들어오는 지불을 거부한다. EVM의 설계 결정으로 인해 두 가지 예외가 있다: coinbase 지불과 `SELFDESTRUCT` 상속은 fallback 함수가 `payable`로 선언되지 않아도 지불되지만, 어쨌든 코드 실행이 이러한 지불의 일부가 아니기 때문에 이치에 맞다.

이제 두 가지 특별한 함수 `receive`와 `fallback`의 동작을 살펴보자:

**receive()**

`receive` 함수는 컨트랙트가 이더를 받을 수 있게 해주는 것이다. 일반적으로 일반 이더 전송(`.send()` 또는 `.transfer()`를 사용하여 수행되는 것과 같은) 중에 빈 calldata로 호출을 받을 때 트리거된다. `receive() external payable { ... }`로 선언되며, 인수를 가질 수 없고 값을 반환할 수 없지만, virtual일 수 있고, override할 수 있으며, 수정자를 가질 수 있다.

**fallback()**

`fallback` 함수는 다른 함수 시그니처와 일치하지 않는 데이터로 컨트랙트가 호출될 때 실행된다. `fallback() external [payable]` 또는 `fallback(bytes calldata input) external [payable] returns (bytes memory output)`을 사용하여 선언할 수 있다. `fallback` 함수가 input 매개변수를 포함하면, 컨트랙트에 전송된 전체 데이터(`msg.data`와 동등)를 포함한다. `receive` 함수와 유사하게, `fallback` 함수는 payable이고 virtual일 수 있으며, override할 수 있고, 수정자를 포함할 수 있다. 오늘날에는 주로 *프록시 패턴:* 스마트 컨트랙트 업그레이드 가능성을 활성화하는 디자인 패턴을 구현하는 데 사용된다.

> **참고**
>
> 컨트랙트에 `receive` 함수가 없지만 payable `fallback` 함수가 있는 경우, 이러한 전송 중에 `fallback` 함수가 실행된다. 컨트랙트에 `receive` 함수도 payable `fallback` 함수도 없는 경우, 이더를 받을 수 없으며 트랜잭션은 예외와 함께 되돌아간다.

### 컨트랙트 생성자

한 번만 사용되는 특별한 함수가 있다. 컨트랙트가 생성될 때, *생성자 함수*가 존재하면 그것도 실행하여 컨트랙트의 상태를 초기화한다. 생성자는 컨트랙트 생성과 동일한 트랜잭션에서 실행된다. 생성자 함수는 선택 사항이다; `Faucet` 예제에는 생성자가 없다는 것을 알 수 있다.

생성자는 `constructor` 키워드를 통해 지정할 수 있다. 다음과 같이 보인다:

```solidity
pragma 0.8.26
// SPDX-License-Identifier: GPL-3.0
contract MEContract {
 address owner;
 constructor () { // 이것이 생성자이다
  owner = msg.sender;
 }
}
```

컨트랙트의 생명 주기는 EOA 또는 컨트랙트 계정에서의 생성 트랜잭션으로 시작한다. 생성자가 있는 경우, 컨트랙트가 생성될 때 컨트랙트의 상태를 초기화하기 위해 컨트랙트 생성의 일부로 실행되고, 그런 다음 폐기된다.

> **참고**
>
> 생성자는 payable로 표시될 수도 있다. 이것은 컨트랙트 생성 트랜잭션과 함께 ETH를 보내려는 경우에 필요하다. 생성자가 payable이 아닌 경우, 배포 중에 전송된 ETH는 트랜잭션이 되돌아가게 한다.

### 함수 수정자

솔리디티는 *함수 수정자*라는 특별한 유형의 함수를 제공한다. 함수 선언에 수정자 이름을 추가하여 함수에 수정자를 적용한다. 수정자는 컨트랙트 내의 많은 함수에 적용되는 조건을 만드는 데 가장 자주 사용된다. `destroy` 함수에 이미 접근 제어 문이 있다. 그 조건을 표현하는 함수 수정자를 만들어 보자:

```solidity
modifier onlyOwner {
 require(msg.sender == owner);
 _;
}
```

`onlyOwner`라는 이름의 이 함수 수정자는, 컨트랙트의 `owner`로 저장된 주소가 트랜잭션의 `msg.sender` 주소와 동일해야 하는 조건을 수정하는 모든 함수에 설정한다. 이것은 접근 제어를 위한 기본 디자인 패턴으로, 컨트랙트의 소유자만이 `onlyOwner` 수정자가 있는 모든 함수를 실행할 수 있게 한다.

함수 수정자에 독특한 문법적 "플레이스홀더"가 있다는 것을 알아차렸을 수 있다: 밑줄 다음에 세미콜론(`_;`). 이 플레이스홀더는 수정되는 함수의 코드로 대체된다. 본질적으로, 수정자는 수정된 함수를 "감싸고", 밑줄 문자로 식별된 위치에 코드를 배치한다.

수정자를 적용하려면, 함수 선언에 그 이름을 추가한다. 하나 이상의 수정자가 함수에 적용될 수 있다; 쉼표로 구분된 목록으로 선언된 순서대로 적용된다.

`onlyOwner` 수정자를 사용하는 `changeOwner` 함수를 정의해 보자:

```solidity
function changeOwner(address newOwner) public onlyOwner {
 require(newOwner != address(0), "New owner address not set");
 owner = newOwner;
}
```

함수 수정자의 이름(`onlyOwner`)은 `public` 키워드 뒤에 있으며, `changeOwner` 함수가 `onlyOwner` 수정자에 의해 수정되었음을 알려준다. 본질적으로, 이것은 "소유자만이 새 소유자 주소를 설정할 수 있다"로 읽을 수 있다. 실제로, 결과 코드는 `onlyOwner`의 코드를 `changeOwner` 주위에 "감싸는" 것과 동등하다.

함수 수정자는 함수에 대한 전제 조건을 작성하고 일관되게 적용할 수 있게 해주어 코드를 읽기 쉽게 만들고, 결과적으로 보안을 위해 감사하기 쉽게 만들기 때문에 매우 유용한 도구이다. 접근 제어에 가장 자주 사용되지만, 상당히 다재다능하며 다양한 다른 목적에 사용될 수 있다.

### 컨트랙트 상속

솔리디티의 `contract` 객체는 기본 컨트랙트를 추가 기능으로 확장하는 메커니즘인 *상속*을 지원한다. 상속을 사용하려면, `is` 키워드로 부모 컨트랙트를 지정한다:

```solidity
contract Child is Parent {
 ...
}
```

이 구조로, `Child` 컨트랙트는 `Parent`의 모든 메서드, 기능, 변수를 상속한다. 솔리디티는 또한 `is` 키워드 뒤에 쉼표로 구분된 컨트랙트 이름으로 지정할 수 있는 다중 상속도 지원한다:

```solidity
contract Child is Parent1, Parent2 {
 ...
}
```

`Parent1.functionName()`과 같이 컨트랙트를 명시적으로 지정하거나, 평탄화된 상속 계층에서 한 수준 위의 함수를 호출하려면 `super.functionName()`을 사용하여 상속 체인 상위에 있는 함수를 호출할 수 있다.

컨트랙트 상속을 통해 모듈성, 확장성, 재사용을 달성하는 방식으로 컨트랙트를 작성할 수 있다. 가장 일반적인 기능을 구현하는 간단한 컨트랙트로 시작한 다음, 더 전문화된 컨트랙트에서 해당 기능을 상속하여 확장한다.

`Faucet` 컨트랙트에서, 생성 시 할당된 소유자에 대한 접근 제어를 도입했다. 이 기능은 상당히 일반적이다: 많은 컨트랙트가 이것을 가질 것이다. 일반 컨트랙트로 정의한 다음, 상속을 사용하여 `Faucet` 컨트랙트로 확장할 수 있다. 예제를 풍부하게 하기 위해, 접근 제어와 함께 일시 중지 가능 기능을 추가해 보자.

`owner` 변수를 가지고 컨트랙트의 생성자에서 설정하는 기본 컨트랙트 `Owned`를 정의하는 것으로 시작한다:

```solidity
contract Owned {
    address owner;
    // Contract constructor: set owner
    constructor() {
        owner = msg.sender;
    }
    // Access control modifier
    modifier onlyOwner {
        require(msg.sender == owner);
        _;
    }
}
```

다음으로, `Owned`를 상속하는 기본 컨트랙트 `Pausable`을 정의한다:

```solidity
contract Pausable is Owned {
    bool paused;
    // Status check modifier
    modifier whenNotPaused {
        require(paused == false);
        _;
    }
    // Functions to pause/unpause user operations
    function pause() public onlyOwner {
        paused = true;
    }
    function unpause() public onlyOwner {
        paused = false;
    }
}
```

보시다시피, `Pausable` 컨트랙트는 `Owned`에 정의된 `onlyOwner` 함수 수정자를 사용할 수 있다. 또한 간접적으로 `Owned`에 정의된 `owner` 주소 변수와 생성자도 사용한다. 상속은 각 컨트랙트를 더 간단하게 만들고 특정 기능에 집중하게 하여, 세부 사항을 모듈 방식으로 관리할 수 있게 한다.

이제 `Owned` 컨트랙트를 더 확장하여, `Faucet`에서 그 기능을 상속할 수 있다:

```solidity
contract Faucet is Pausable {
    // Give out ether to anyone who asks
    function withdraw(uint _withdrawAmount, address payable _to) public whenNotPaused {
        // Limit withdrawal amount
        require(_withdrawAmount <= 0.1 ether);
        // Send the amount to the address that requested it
        _to.transfer(_withdrawAmount);
    }
    // Accept any incoming amount
    receive() external payable {}
}
```

`Owned`를 상속하는 `Pausable`을 상속함으로써, `Faucet` 컨트랙트는 이제 `whenNotPaused` 수정자를 사용할 수 있으며, 그 출력은 `Owned` 컨트랙트 생성자를 통해 정의된 소유자에 의해 제어될 수 있다. 기능은 해당 함수가 `Faucet` 내에 있는 것과 동일하지만, 이 모듈형 아키텍처 덕분에 다른 컨트랙트에서 함수와 수정자를 다시 작성하지 않고도 재사용할 수 있다. 코드 재사용과 모듈성은 코드를 더 깔끔하고, 읽기 쉽고, 감사하기 쉽게 만든다.

때로는 상속된 컨트랙트의 일부 기능을 변경해야 할 수도 있다. 다행히도, 솔리디티는 적절한 기능을 제공한다: 함수 오버라이딩. `virtual`로 선언된 함수는 상속 체인에서 상위에 있는 컨트랙트에 의해 오버라이드될 수 있어, 상속 접근 방식을 매우 유연하게 유지한다.

예제를 살펴보자. 일시 중지 가능 기능을 일방향으로 만들고 싶다고 가정하자: 일단 일시 중지되면 컨트랙트는 더 이상 일시 중지를 해제할 수 없다. 이를 위해, `Pausable` 컨트랙트의 `unpause` 함수를 `virtual`로 표시하고 `Faucet` 컨트랙트에서 `override` 속성으로 `unpause` 함수를 다시 선언하여, revert하는 새로운 원하는 동작을 정의해야 한다:

```solidity
contract Pausable is Owned {
    bool paused;
    // Status check modifier
    modifier whenNotPaused {
        require(paused == false);
        _;
    }
    // Functions to pause/unpause user operations
    function pause() public virtual onlyOwner {
        paused = true;
    }
    function unpause() public virtual onlyOwner {
        paused = false;
    }
}
contract Faucet is Pausable {
    // Give out ether to anyone who asks
    function withdraw(uint _withdrawAmount, address payable _to) public whenNotPaused {
        // Limit withdrawal amount
        require(_withdrawAmount <= 0.1 ether);
        // Send the amount to the address that requested it
        _to.transfer(_withdrawAmount);
    }
    function unpause() public view override onlyOwner {
        revert("Disabled feature");
    }
    // Accept any incoming amount
    receive() external payable {}
}
```

보시다시피, `Faucet`의 `unpause()` 함수는 `override` 키워드로 선언되어야 한다. `Pausable` 컨트랙트에서 일관성을 위해 `pause`와 `unpause` 함수 모두를 virtual로 표시했지만, 우리의 경우에는 `unpause`만 변경하면 되었다.

> **참고**
>
> 솔리디티에서 함수를 오버라이드할 때, 가시성을 더 접근 가능하게만 만들 수 있다—구체적으로, external에서 public으로 변경할 수 있지만 그 반대는 안 된다. 변경 가능성의 경우, nonpayable에서 view 또는 pure로(`unpause`에서 했던 것처럼), view에서 pure로 강화할 수 있다. 하지만 한 가지 큰 예외가 있다: 함수가 payable로 표시되면 그대로 유지해야 한다—다른 것으로 변경할 수 없다.

### 다중 상속

솔리디티에서 다중 상속을 사용할 때, 컨트랙트가 상속되는 순서를 파악하기 위해 C3 선형화 알고리즘이라는 것에 의존한다. 이 알고리즘은 상속 순서가 엄격하고 예측 가능하도록 보장하여, 순환 상속과 같은 문제를 피하는 데 도움이 된다. 가장 간단한 형태로, 함수를 찾을 때 기본 컨트랙트가 확인되는 순서를 파악하며, 이 순서는 오른쪽에서 왼쪽으로 간다. 이는 오른쪽의 컨트랙트가 "가장 파생된" 것으로 간주됨을 의미한다. 예를 들어, 컨트랙트 선언 `contract C is A, B { }`에서 컨트랙트 B는 컨트랙트 A보다 더 파생되었다.

이제, C3 선형화를 사용하는 것 외에도, 솔리디티에는 추가적인 보호 장치가 있다. 한 가지 핵심 규칙은 여러 컨트랙트가 동일한 함수를 가지고 있으면 어떤 컨트랙트가 오버라이드되는지 명시적으로 명시해야 한다는 것이다. 예제를 살펴보자:

```solidity
contract A {
    function foo() public virtual returns(string memory){
        return "A";
    }
}
contract B {
    function foo() public virtual returns(string memory){
        return "B";
    }
}
contract C is A, B {
}
```

언뜻 보기에, C3 선형화가 모든 것을 처리해야 하므로 잘 작동해야 할 것 같다. 하지만 실제로는 컴파일되지 않는다. 솔리디티는 "TypeError: Derived contract must override function `foo`. Two or more base classes define a function with the same name and parameter types."라는 오류를 throw한다. 문제를 해결하려면 다음과 같이 A와 B 모두에서 `foo()` 함수를 명시적으로 오버라이드해야 한다:

```solidity
contract C is A, B {
    function foo() public override(A, B) returns(string memory){
        return "C";
    }
}
```

따라서 솔리디티가 C3 선형화를 사용하더라도, 솔리디티가 함수 오버라이드를 명시적으로 처리하도록 강제하기 때문에 코딩하는 동안 대부분의 경우 걱정할 필요가 없다.

그러나 C3 선형화가 중요한 한 가지 경우는 솔리디티가 생성자 실행 순서를 결정할 때이다. 생성자는 C3 선형화된 순서를 따르지만, 여기에 반전이 있다: 역순으로 실행된다. 생각해보면 이치에 맞다: 가장 파생된 컨트랙트의 생성자가 마지막에 실행되어야 하는데, 이전 생성자가 설정한 것을 오버라이드할 수 있기 때문이다. 예제를 살펴보자:

```solidity
contract Base{
    uint x;
}
contract Derived1 is Base{
    constructor(){
        x = 1;
    }
}
contract Derived2 is Base{
    constructor(){
        x = 2;
    }
}
contract Derived3 is Derived1, Derived2 {
    uint public y;
    constructor() Derived1() Derived2() {
        y = x;
    }
}
```

이 경우, `Derived2`의 생성자가 마지막에 실행되어 `x`를 2로 설정하기 때문에 `y`의 값은 예상대로 2가 된다.

명심해야 할 중요한 점은 생성자 인수를 제공하는 순서가 실행 순서에 영향을 미치지 않는다는 것이다. 예를 들어, 다음과 같이 생성자 호출을 뒤집을 수 있다:

```solidity
contract Derived3 is Derived1, Derived2 {
    uint public y;
    constructor() Derived2() Derived1() { // 여기서 순서를 바꿨다
        y = x;
    }
}
```

생성자에서 순서를 변경했지만, 결과는 여전히 동일할 것이다. 생성자 실행 순서는 우리가 생성자를 호출하는 순서가 아니라 C3 선형화에 의해 결정되기 때문에 `y`의 값은 2가 될 것이다.

마지막 주의 사항: 솔리디티의 다중 상속을 위한 C3 선형화 사용은 `super` 키워드가 예상치 못한 방식으로 동작하게 만들 수 있다. 때때로, `super`를 호출하면 직접 부모 대신 형제 클래스의 함수가 트리거될 수 있다. 이는 상속 체인에 나열하지도 않은 클래스에서 메서드가 호출되는 놀라운 결과로 이어질 수 있다. 약간의 엣지 케이스이므로 너무 깊이 들어가지 않겠지만, 복잡한 상속 설정을 가진 컨트랙트에서 `super` 키워드를 사용할 때 이 점을 명심해야 한다.

### 오류 처리

컨트랙트 호출은 종료되고 오류를 반환할 수 있다. 솔리디티에서 오류 처리는 세 가지 함수로 처리된다: `assert`, `require`, `revert`.

컨트랙트가 오류로 종료되면 모든 상태 변경(변수, 잔액 등의 변경)이 되돌려지며, 하나 이상의 컨트랙트가 호출된 경우 컨트랙트 호출 체인 전체가 되돌려진다. 이는 트랜잭션이 *원자적*임을 보장하며, 성공적으로 완료되거나 상태에 영향을 미치지 않고 완전히 되돌려진다는 것을 의미한다.

`assert`와 `require` 함수는 동일한 방식으로 작동하며, 조건을 평가하고 조건이 false이면 오류와 함께 실행을 중지한다. 관례상, `assert`는 결과가 true일 것으로 예상될 때 사용되며, 이는 내부 조건을 테스트하기 위해 `assert`를 사용한다는 것을 의미한다. 비교하면, `require`는 입력(함수 인수 또는 트랜잭션 필드와 같은)을 테스트할 때 사용되며, 해당 조건에 대한 기대치를 설정한다. `assert`는 실패할 때 `require`와 다르게 동작한다는 점도 주목할 가치가 있다: 남은 모든 가스를 소비한다. 이로 인해 트리거될 때 더 비싸지며, 절대로 깨지지 않아야 하는 불변 조건에 대해 일반적으로 예약하는 이유 중 하나이다.

`onlyOwner` 함수 수정자에서 메시지 발신자가 컨트랙트의 소유자인지 테스트하기 위해 `require`를 사용했다:

```solidity
require(msg.sender == owner);
```

`require` 함수는 *게이트 조건*으로 작용하여, 충족되지 않으면 함수의 나머지 실행을 방지하고 오류를 생성한다. 오류의 이유를 표시하는 데 사용할 수 있는 유용한 텍스트 메시지도 포함할 수 있다. 오류 메시지는 트랜잭션 로그에 기록되며, 사용자가 오류가 무엇이고 어떻게 수정하는지 알 수 있게 하여 사용자 경험을 개선하기 위해 채택이 권장된다. 따라서 `require` 함수에 오류 메시지를 추가하여 코드를 개선할 수 있다:

```solidity
require(msg.sender == owner, "Only the contract owner can call this function");
```

`revert` 함수는 컨트랙트의 실행을 중지하고 모든 상태 변경을 되돌린다. 두 가지 방법으로 사용할 수 있다: 괄호 없이 사용자 정의 오류가 직접 전달되는 문장으로, 또는 문자열 인수를 취하는 괄호가 있는 함수로. 사용자 정의 오류는 가스 비용 측면에서 훨씬 저렴하며, 오류 문자열과 사용자 정의 오류 모두 트랜잭션 로그에 기록된다:

```solidity
revert();
revert("Error string");
revert CustomError(arg1, arg2);
```

컨트랙트의 특정 조건은 명시적으로 확인하는지 여부와 관계없이 오류를 생성한다. 예를 들어, `Faucet` 컨트랙트에서 인출 요청을 충족할 충분한 이더가 있는지 확인하지 않는다. 전송을 수행할 잔액이 부족하면 `transfer` 함수가 오류와 함께 실패하고 트랜잭션을 되돌리기 때문이다:

```solidity
payable(msg.sender).transfer(withdrawAmount);
```

그러나 명시적으로 확인하고 실패 시 명확한 오류 메시지를 제공하는 것이 더 나을 수 있다. 전송 전에 `require` 문을 추가하여 이를 수행할 수 있다:

```solidity
require(this.balance >= withdrawAmount,
 "Insufficient balance in faucet for withdrawal request");
payable(msg.sender).transfer(withdrawAmount);
```

이와 같은 추가 오류 확인 코드는 가스 소비를 약간 증가시키지만, 생략할 때보다 더 나은 오류 보고를 제공한다. 가스 소비 최소화는 이더리움 메인넷의 높은 비용으로 인해 필수 활동이었지만, EIP-4844의 도입으로 그 비용이 크게 줄어들어 오늘날 가스 소비가 덜 긴급한 문제가 되었다. 그러나 가스 효율성과 철저한 오류 확인 사이에서 적절한 균형을 맞추는 것은 여전히 중요하다.

솔리디티는 try/catch 기능을 통해 오류 처리에 대한 더 많은 제어권을 제공한다. 이것은 외부 컨트랙트를 호출할 때 오류를 더 우아하게 처리할 수 있게 해주는 매우 편리한 기능이다. 무언가 잘못될 때 전체 트랜잭션이 실패하고 되돌아가는 대신, 오류를 잡아서 다음에 무엇을 할지 결정할 수 있다. try/catch를 사용할 때, 기본적으로 외부 호출을 `try` 블록에 감싼다. 호출이 성공하면, `try` 블록 내부의 코드가 정상적으로 실행된다. 하지만 무언가 잘못되면—호출된 컨트랙트가 가스가 부족해지거나, `require` 문에 걸리거나, 예외를 throw하는 경우—코드가 `catch` 블록으로 점프하여 오류를 처리할 수 있다.

다음은 간단한 예제이다:

```solidity
function sampleExternalCall(address target, uint amount) public {
    try ITargetContract(target).someFunction(amount) {
        // 호출이 성공하면 이것이 실행된다
        emit Success("Call succeeded!");
    } catch {
        // 호출이 실패하면 이것이 실행된다
        emit Error("Call failed!");
    }
}
```

오류 유형에 따라 다른 방식으로 오류를 잡을 수 있다. 기본 `catch` 블록은 모든 오류를 잡지만, 특정 오류도 잡을 수 있다. 예를 들어, `catch Error(string memory reason)`을 사용하여 오류 문자열을 반환하는 오류를 잡거나, `catch (bytes memory lowLevelData)`로 데이터를 반환하지 않는 저수준 오류를 처리할 수 있다. 또한, `catch Panic(uint errorCode)`를 사용하여 오버플로우나 0으로 나누기와 같은 더 심각한 패닉 오류를 잡을 수 있다.

try/catch는 외부 호출에서만 작동한다. 동일한 컨트랙트 내의 내부 함수 호출에는 도움이 되지 않는다. 동일한 컨트랙트의 함수가 실패하면 여전히 평소처럼 되돌아가며, try/catch로 잡을 수 없다.

### 이벤트

트랜잭션이 완료되면(성공 여부에 관계없이), 트랜잭션 영수증을 생성한다. 트랜잭션 영수증에는 트랜잭션 실행 중에 발생한 작업에 대한 정보를 제공하는 로그 항목이 포함되어 있다. *이벤트*는 이러한 로그를 구성하는 데 사용되는 솔리디티 고급 객체이다.

이벤트는 특정 이벤트를 "감시"하고 사용자 인터페이스에 보고하거나 기본 컨트랙트의 이벤트를 반영하여 애플리케이션 상태를 변경할 수 있는 라이트 클라이언트와 DApp 서비스에 특히 유용하다.

이벤트 객체는 직렬화되어 블록체인의 트랜잭션 로그에 기록되는 인수를 취한다. 인수 앞에 `indexed` 키워드를 제공하면 값이 애플리케이션에서 검색하거나 필터링할 수 있는 인덱스된 테이블(해시 테이블)의 일부가 된다.

#### 이벤트 추가하기

지금까지 `Faucet` 예제에 이벤트를 추가하지 않았으므로, 추가해 보자. 두 개의 이벤트를 추가할 것이다: 하나는 인출을 기록하고 다른 하나는 입금을 기록한다. 이러한 이벤트를 각각 `Withdrawal`과 `Deposit`이라고 부를 것이다. 먼저, `Faucet` 컨트랙트에서 이벤트를 정의한다:

```solidity
contract Faucet is Pausable {
 event Withdrawal(address indexed to, uint amount);
 event Deposit(address indexed from, uint amount);
 [...]
}
```

`Faucet`에 접근하기 위해 구축된 모든 사용자 인터페이스에서 검색 및 필터링을 허용하기 위해 주소를 `indexed`로 만들었다.

다음으로, `emit` 키워드를 사용하여 트랜잭션 로그에 이벤트 데이터를 통합한다:

```solidity
// Give out ether to anyone who asks
function withdraw(uint withdrawAmount) public {
 [...]
 payable(msg.sender).transfer(withdrawAmount);
 emit Withdrawal(msg.sender, withdrawAmount);
}
// Accept any incoming amount
receive() external payable {
 emit Deposit(msg.sender, msg.value);
}
```

결과 *Faucet.sol* 컨트랙트는 예제 7-3과 같다.

**예제 7-3. Faucet.sol: 이벤트가 있는 수정된 Faucet 컨트랙트**

```solidity
// Version of Solidity compiler this program was written for
pragma solidity 0.8.26;
// SPDX-License-Identifier: GPL-3.0
contract Owned {
    address owner;
    // Contract constructor: set owner
    constructor() {
        owner = msg.sender;
    }
    // Access control modifier
    modifier onlyOwner {
        require(msg.sender == owner);
        _;
    }
}
contract Pausable is Owned {
    event Paused();
    event Unpaused();
    bool paused;
    // Status check modifier
    modifier whenNotPaused {
        require(paused == false);
        _;
    }
    // Functions to pause/unpause user operations
    function pause() public onlyOwner {
        paused = true;
        emit Paused();
    }
    function unpause() public onlyOwner {
        paused = false;
        emit Unpaused();
    }
}
contract Faucet is Pausable {
    event Withdrawal(address indexed to, uint amount);
    event Deposit(address indexed from, uint amount);
    // Give out ether to anyone who asks
    function withdraw(uint withdrawAmount) public whenNotPaused {
        // Limit withdrawal amount
        require(withdrawAmount <= 0.1 ether);
        // Send the amount to the address that requested it
        payable(msg.sender).transfer(withdrawAmount);
        emit Withdrawal(msg.sender, withdrawAmount);
    }
    // Accept any incoming amount
    receive() external payable {
        emit Deposit(msg.sender, msg.value);
    }
}
```

#### 이벤트 캐칭하기

코드로 온체인 이벤트를 캐치하는 방법을 살펴보자. 구체적으로, 이더리움 메인넷에서 USDT 토큰 전송을 모니터링하는 스크립트를 작성할 것이다. 이를 위해 Web3 라이브러리가 필요하며, web3.js가 처음으로 인기를 얻었지만, 최근 몇 년간 ethers.js가 이를 추월했다. 개발자로서 우리는 ethers.js를 선호하므로, 여기서도 그것을 사용할 것이다.

먼저, 프로젝트를 설정하자. 새 프로젝트 폴더를 만든 다음, 다음을 실행하여 ethers 라이브러리를 설치한다:

```bash
npm i ethers
```

다음으로, USDT 컨트랙트의 ABI가 필요하다. [Etherscan](https://oreil.ly/LoWH4)에서 컨트랙트 소스 코드 바로 아래(그림 7-1 참조)에서 가져와 프로젝트 폴더에 저장할 수 있다.

![Etherscan의 USDT ABI 섹션](../images/ch7/maet_0701.png)

**그림 7-1.** Etherscan의 USDT ABI 섹션

이제 이더리움 네트워크에 어떻게 연결할지 이야기하자. 이벤트를 수신하기 때문에 WebSocket 프로바이더가 필요한데, 이는 지속적인 연결이 필요하기 때문이다. 유료 프로바이더(더 안정적이고 빠름) 또는 [ChainList](https://chainlist.org)와 같은 웹사이트에서 찾을 수 있는 무료 공개 프로바이더 중에서 선택할 수 있다. 우리 예제에서는 속도 제한이 있을 수 있지만 공개 프로바이더도 충분할 것이다.

> **참고**
>
> 이벤트를 수신하려면 RPC 엔드포인트가 아닌 WebSocket이 필요하다. RPC 엔드포인트는 함수 호출이나 데이터 가져오기와 같은 단일 요청에 적합하지만, 이벤트를 캐치하려면 클라이언트와 서버 간의 열린 통신 라인을 유지할 수 있게 해주는 WebSocket 연결이 필요하다.

이제 코드를 살펴보자:

```javascript
const ethers = require("ethers");
const ABI = require("./USDTabi.json"); // etherscan에서 가져온 ABI
const usdtAddress = "0xdac17f958d2ee523a2206206994597c13d831ec7"; // USDT Contract
const wssProviderURL = "wss://ethereum-rpc.publicnode.com"; // 공개 websocket 프로바이더
const wssProvider = new ethers.providers.WebSocketProvider(wssProviderURL);
const usdtContract = new ethers.Contract(usdtAddress, ABI, wssProvider);
async function getTransfer(){
    usdtContract.on("Transfer", (from, to, value, event)=>{
        let transferEvent ={
            from: from,
            to: to,
            value: value,
            eventData: event,
        }
        console.log(JSON.stringify(transferEvent, null, 2))
    })
}
getTransfer()
```

USDT 주소와 WebSocket 프로바이더 URL을 선언한 후, 스크립트는 ethers 라이브러리를 사용하여 새 WebSocketProvider 인스턴스를 생성하면서 시작된다. 이 프로바이더는 지정한 WebSocket URL을 통해 이더리움 네트워크에 연결한다. 다음으로, `ethers.Contract` 클래스를 사용하여 USDT 스마트 컨트랙트의 인스턴스를 생성한다. USDT 주소, ABI, WebSocket 프로바이더를 전달한다.

이제 스크립트의 핵심에 도달한다. USDT 컨트랙트에서 발생하는 `Transfer` 이벤트를 캐치하기 위해 컨트랙트 인스턴스에 이벤트 리스너를 설정한다. `Transfer` 이벤트가 발생할 때마다, 이 리스너는 전송과 관련된 네 개의 매개변수를 받는 콜백 함수를 트리거한다. 콜백 내에서 전송 세부 정보를 가져와 객체에 래핑한 다음, 이 객체를 JSON 문자열로 포맷한다. 마지막으로, `console.log`를 사용하여 해당 JSON 문자열을 콘솔에 출력하여 각 전송에서 정확히 무슨 일이 일어나는지 실시간으로 볼 수 있다.

다음은 스크립트의 샘플 출력이다:

```json
{
  "from": "0xc169e35abb35f8e712eCF9F6d9465C96962CA383",
  "to": "0x7E73F680243A93a9D98C5Ce4b349451805fc37ca",
  "value": {
    "type": "BigNumber",
    "hex": "0x55b27b90"
  },
  "eventData": {
    "blockNumber": 20687220,
    "blockHash": "0xa5c3c518d7246e516e076ef8d43c387dcb54d06702e9e059c583ce28a7a271b8",
    "transactionIndex": 166,
    "removed": false,
    "address": "0xdAC17F958D2ee523a2206206994597C13D831ec7",
    "data": "0x0000000000000000000000000000000000000000000000000000000055b27b90",
    "topics": [
      "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
      "0x000000000000000000000000c169e35abb35f8e712ecf9f6d9465c96962ca383",
      "0x0000000000000000000000007e73f680243a93a9d98c5ce4b349451805fc37ca"
    ],
    "transactionHash":
      "0xb527a5a18f10ed9b65dda7a914715a0b0bbfd6db053d8f6b35805ad49a588cfd",
    "logIndex": 300,
    "event": "Transfer",
    "eventSignature": "Transfer(address,address,uint256)",
    "args": [
      "0xc169e35abb35f8e712eCF9F6d9465C96962CA383",
      "0x7E73F680243A93a9D98C5Ce4b349451805fc37ca",
      {
        "type": "BigNumber",
        "hex": "0x55b27b90"
      }
    ]
  }
}
```

이와 같은 이벤트는 오프체인 통신뿐만 아니라 디버깅에도 매우 유용하다. 개발 중에 트랜잭션 영수증의 "logs" 항목에서 이러한 이벤트를 찾을 수 있으며, 예상대로 작동하지 않을 때 생명의 은인이 될 수 있다.

### 다른 컨트랙트 호출하기

컨트랙트 내에서 다른 컨트랙트를 호출하는 것은 매우 유용하지만 잠재적으로 위험한 작업이다. 이를 달성할 수 있는 다양한 방법을 살펴보고 각 방법의 위험을 평가할 것이다. 간단히 말해, 위험은 호출하려는 컨트랙트나 컨트랙트를 호출하는 컨트랙트에 대해 많이 알지 못할 수 있다는 사실에서 발생한다. 스마트 컨트랙트를 작성할 때, 주로 EOA를 다루게 될 것으로 예상할 수 있지만, 임의로 복잡하고 악의적일 수 있는 컨트랙트가 코드를 호출하거나 코드에 의해 호출되는 것을 막을 수 없다는 점을 명심해야 한다.

#### 새 인스턴스 생성하기

다른 컨트랙트를 호출하는 가장 안전한 방법은 직접 그 컨트랙트를 생성하는 것이다. 그렇게 하면 인터페이스와 동작을 확실히 알 수 있다. 이를 위해, 다른 객체 지향 언어에서처럼 `new` 키워드를 사용하여 간단히 인스턴스화할 수 있다. 솔리디티에서 `new` 키워드는 블록체인에 컨트랙트를 생성하고 참조하는 데 사용할 수 있는 객체를 반환한다. `Token`이라는 다른 컨트랙트 내에서 `Faucet` 컨트랙트를 생성하고 호출하고 싶다고 가정해 보자:

```solidity
contract Token is Pausable {
    Faucet _faucet;
    constructor() {
        _faucet = new Faucet();
    }
}
```

컨트랙트 생성을 위한 이 메커니즘은 컨트랙트의 정확한 타입과 인터페이스를 알 수 있도록 보장한다. `Faucet` 컨트랙트는 `Token`의 범위 내에서 정의되어야 하며, 정의가 다른 파일에 있는 경우 `import` 문으로 수행할 수 있다:

```solidity
import "Faucet.sol";
contract Token is Pausable {
    Faucet _faucet;
    constructor() {
        _faucet = new Faucet();
    }
}
```

생성 시 이더 전송 값을 선택적으로 지정하고 새 컨트랙트의 생성자에 인수를 전달할 수 있다:

```solidity
import "Faucet.sol";
contract Token is Pausable {
    Faucet _faucet;
    constructor() {
        _faucet = new Faucet{value: 0.5 ether}();
    }
}
```

이것은 `Faucet` 생성자가 payable이어야 한다는 점에 유의해야 한다!

그런 다음 `Faucet` 함수를 호출할 수도 있다. 이 예제에서는 `Token`의 `changeOwner` 함수 내에서 `Faucet`의 `changeOwner` 함수를 호출한다:

```solidity
import "Faucet.sol";
contract Token is Pausable {
    Faucet _faucet;
    constructor() {
         _faucet = new Faucet{value: 0.5 ether}();
    }
    function changeOwner(address newOwner) onlyOwner {
        _faucet.changeOwner(newOwner);
    }
}
```

`Token` 컨트랙트의 소유자인 동안, `Token` 컨트랙트 자체가 새 `Faucet` 컨트랙트를 소유하며 당신이 아니라는 것을 이해하는 것이 중요하다! 이 장 앞부분에서 보았듯이, 외부 호출 중에 `msg.sender`가 변경된다; 우리의 경우, `Faucet` 실행 컨텍스트에서 그것은 `Token` 주소가 될 것이다.

#### 기존 인스턴스 주소 지정하기

컨트랙트를 호출하는 또 다른 방법은 컨트랙트의 기존 인스턴스 주소를 캐스팅하는 것이다. 이 방법을 사용하면 기존 인스턴스에 알려진 인터페이스를 적용한다. 따라서 주소를 지정하는 인스턴스가 실제로 가정하는 타입인지 확실히 아는 것이 매우 중요하다. 예제를 살펴보자:

```solidity
import "Faucet.sol";
contract Token is Pausable {
    Faucet _faucet;
    constructor(address _f) {
        _faucet = Faucet(_f);
        _faucet.withdraw(0.1 ether)
    }
}
```

여기서 생성자의 인수로 제공된 주소 `_f`를 받아 `Faucet` 객체로 캐스팅한다. 이것은 이전 메커니즘보다 훨씬 더 위험한데, 그 주소가 실제로 `Faucet` 객체인지 확실히 알 수 없기 때문이다. `withdraw`를 호출할 때, 우리의 `Faucet` 선언과 동일한 인수를 받아들이고 동일한 코드를 실행한다고 가정하지만, 확실할 수 없다. 우리가 아는 한, 이 주소의 `withdraw` 함수는 이름이 같더라도 우리가 예상하는 것과 완전히 다른 것을 실행할 수 있다. 따라서 입력으로 전달된 주소를 사용하고 특정 객체로 캐스팅하는 것은 직접 컨트랙트를 생성하는 것보다 훨씬 더 위험하다.

#### 로우 call, delegatecall, staticcall

솔리디티는 다른 컨트랙트를 호출하기 위한 더 "저수준" 함수를 제공한다. 이것들은 동일한 이름의 EVM 연산 코드에 직접 해당하며 컨트랙트 간 호출을 수동으로 구성할 수 있게 해준다. 따라서 다른 컨트랙트를 호출하기 위한 가장 유연하고 *또한* 가장 위험한 메커니즘을 나타낸다. 두 가지 값을 반환한다: 작업이 성공했는지 여부를 나타내는 `bool success`와 반환 데이터를 포함하는 `bytes memory data`.

다음은 `call` 메서드를 사용하는 동일한 예제이다:

```solidity
contract Token is Pausable {
    constructor(address _faucet) {
        _faucet.call(abi.encodeWithSignature("withdraw(uint256)", 0.1 ether));
    }
}
```

보시다시피, 이 유형의 `call`은 컨트랙트 컨텍스트 내에서 원시 트랜잭션을 구성하는 것과 매우 유사한 함수에 대한 *맹목적* 호출이다. `call` 함수는 문제가 있으면 `false`를 반환하므로, 오류 처리를 위해 반환 값을 평가할 수 있다:

```solidity
contract Token2 is Pausable {
    constructor(address _faucet) {
        (bool res, ) = _faucet.call(
            abi.encodeWithSignature("withdraw(uint256)", 0.1 ether)
        );
        if (!res) {
            revert("Withdrawal from faucet failed");
        }
    }
}
```

`call`의 변형은 `staticcall`과 `delegatecall`이다. "주소 객체" 섹션에서 언급했듯이, `staticcall`은 상태 변경이 없음을 보장하는 방식으로 다른 컨트랙트의 함수를 호출한다. 이는 호출된 함수가 상태 변수를 수정하거나, 블록체인의 상태와 상호 작용하거나, 이더를 보낼 수 없음을 의미한다.

`delegatecall`은 `msg` 컨텍스트가 변경되지 않는다는 점에서 `call`과 다르다. 예를 들어, `call`은 `msg.sender`의 값을 호출 컨트랙트로 변경하는 반면, `delegatecall`은 호출 컨트랙트와 동일한 `msg.sender`를 유지한다. 본질적으로, `delegatecall`은 현재 컨트랙트의 실행 컨텍스트 내에서 다른 컨트랙트의 코드를 실행한다. 라이브러리에서 코드를 호출하는 데 가장 자주 사용된다. 또한 다른 곳에 저장된 라이브러리 함수를 사용하는 패턴을 활용하면서 해당 코드가 컨트랙트의 저장소 데이터와 함께 작동하게 할 수 있다; 이것의 명확한 예는 프록시 패턴이다. `delegatecall`은 매우 신중하게 사용해야 한다. 특히 호출하는 컨트랙트가 라이브러리로 설계되지 않은 경우 예상치 못한 효과가 있을 수 있다.

라이브러리와 컨트랙트를 호출하기 위해 `call`과 `delegatecall`이 사용하는 다양한 호출 의미를 보여주기 위해 예제 컨트랙트를 사용해 보자. 예제 7-4에서 이벤트를 사용하여 각 호출의 세부 정보를 기록하고 호출 유형에 따라 호출 컨텍스트가 어떻게 변경되는지 확인한다.

**예제 7-4. CallExamples.sol: 다양한 호출 의미의 예**

```solidity
pragma solidity 0.8.26;
contract CalledContract {
    event callEvent(address sender, address origin, address from);
    function calledFunction() public {
        emit callEvent(msg.sender, tx.origin, address(this));
    }
}
library CalledLibrary {
    event callEvent(address sender, address origin, address from);
    function calledFunction() public {
        emit callEvent(msg.sender, tx.origin, address(this));
    }
}
contract Caller {
    function makeCalls(CalledContract _calledContract) public {
        // CalledContract와 CalledLibrary 직접 호출
        _calledContract.calledFunction();
        CalledLibrary.calledFunction();
        // CalledContract에 대한 address 객체를 사용한 저수준 호출
        (bool res, ) = address(_calledContract).
            call(abi.encodeWithSignature("calledFunction()"));
        require(res);
        (res, ) = address(_calledContract).
            delegatecall(abi.encodeWithSignature("calledFunction()"));
        require(res);
    }
}
```

이 예제에서 보시다시피, 메인 컨트랙트는 라이브러리 `CalledLibrary`와 컨트랙트 `CalledContract`를 호출하는 `Caller`이다. 호출된 라이브러리와 컨트랙트 모두 이벤트 `calledEvent`를 발생시키는 동일한 `calledFunction` 함수를 가지고 있다. 이벤트 `calledEvent`는 세 가지 데이터를 기록한다: `msg.sender`, `tx.origin`, `this`. `calledFunction`이 호출될 때마다, 직접 호출되는지 `delegatecall`을 통해 호출되는지에 따라 다른 실행 컨텍스트(잠재적으로 모든 컨텍스트 변수에 대해 다른 값)를 가질 수 있다.

`Caller`에서 먼저 각각에서 `calledFunction`을 호출하여 컨트랙트와 라이브러리를 직접 호출한다. 그런 다음, 저수준 함수 `call`과 `delegatecall`을 명시적으로 사용하여 `CalledContract.calledFunction`을 호출한다. 이렇게 하면 다양한 호출 메커니즘이 어떻게 동작하는지 볼 수 있다.

컨트랙트를 배포하고 `makeCalls` 함수를 실행하고 이벤트를 캡처해 보자. 명확성을 위해 주소를 레이블(예: `CALLER_CONTRACT_ADDRESS`)로 대체할 것이다.

`makeCalls` 함수를 호출하고 `CalledContract`의 주소를 전달한 다음, 각각의 다른 호출에서 발생한 네 개의 이벤트를 캡처했다. `makeCalls` 함수를 살펴보고 각 단계를 살펴보자.

첫 번째 호출은:

```solidity
_calledContract.calledFunction();
```

여기서 `calledFunction`에 대한 고수준 ABI를 사용하여 `CalledContract.calledFunction`을 직접 호출하고 있다. 발생된 이벤트의 인수는:

```json
{
    sender: 'CALLER_CONTRACT_ADDRESS',
    origin: 'EOA_ADDRESS',
    from: 'CALLED_CONTRACT_ADDRESS'
}
```

보시다시피, `msg.sender`는 `Caller` 컨트랙트의 주소이다. `tx.origin`은 `Caller`에 트랜잭션을 보낸 우리 계정 `web3.eth.accounts[0]`의 주소이다. 이벤트의 마지막 인수에서 볼 수 있듯이 이벤트는 `CalledContract`에 의해 발생되었다.

`makeCalls`의 다음 호출은 라이브러리에 대한 것이다:

```solidity
CalledLibrary.calledFunction();
```

컨트랙트를 호출한 방법과 동일해 보이지만 매우 다르게 동작한다. 발생된 두 번째 이벤트를 살펴보자:

```json
{
    sender: 'EOA_ADDRESS',
    origin: 'EOA_ADDRESS',
    from: 'CALLER_CONTRACT_ADDRESS'
}
```

이번에는 `msg.sender`가 `Caller`의 주소가 아니다. 대신 우리 계정의 주소이며 트랜잭션 origin과 동일하다. 라이브러리를 호출할 때 호출은 항상 `delegatecall`이며 호출자의 컨텍스트 내에서 실행되기 때문이다. 따라서 `CalledLibrary` 코드가 실행될 때, 마치 그 코드가 `Caller` 내에서 실행되는 것처럼 `Caller`의 실행 컨텍스트를 상속했다. 변수 `this`(발생된 이벤트에서 `from`으로 표시됨)는 `CalledLibrary` 내에서 접근되더라도 `Caller`의 주소이다.

저수준 `call`과 `delegatecall`을 사용하는 다음 두 호출은 우리의 기대를 확인하며, 방금 본 것을 반영하는 이벤트를 발생시킨다.

## 가스 고려 사항

14장에서 더 자세히 설명되는 가스는 스마트 컨트랙트 프로그래밍에서 매우 중요한 고려 사항이다. 가스는 이더리움이 트랜잭션이 소비할 수 있는 최대 연산량을 제한하는 리소스이다. 연산 중에 가스 한도를 초과하면 다음과 같은 일련의 이벤트가 발생한다:

- "out of gas" 예외가 throw된다.
- 실행 전 컨트랙트의 상태가 복원된다(되돌림).
- 가스를 지불하는 데 사용된 모든 이더가 트랜잭션 수수료로 가져간다; 환불되지 *않는다*.

### 모범 사례

가스는 트랜잭션을 시작하는 사용자가 지불하므로, 사용자는 가스 비용이 높은 함수를 호출하는 것을 꺼린다. 따라서 컨트랙트 함수의 가스 비용을 최소화하는 것이 프로그래머의 최선의 이익이다. 이를 위해, 함수 호출의 가스 비용을 최소화하기 위해 스마트 컨트랙트를 구성할 때 권장되는 특정 관행이 있다:

**동적 크기 배열 피하기**

함수가 각 요소에 대해 작업을 수행하거나 특정 요소를 검색하는 동적 크기 배열을 통한 모든 루프는 너무 많은 가스를 사용할 위험이 있다. 실제로, 컨트랙트는 원하는 결과를 찾기 전에 또는 모든 요소에 대해 작업하기 전에 가스가 부족해질 수 있으며, 따라서 결과를 전혀 제공하지 않으면서 시간과 이더를 낭비하게 된다.

**다른 컨트랙트 호출 피하기**

다른 컨트랙트를 호출하는 것, 특히 함수의 가스 비용을 알 수 없는 경우, 가스 부족의 위험이 있다. 잘 테스트되지 않고 광범위하게 사용되지 않는 라이브러리 사용을 피해야 한다. 다른 프로그래머로부터 받은 검토가 적을수록 사용 위험이 커진다.

**중복 저장소 접근 피하기**

저장소 변수에 접근하는 것은 읽기든 쓰기든 메모리 변수로 작업하는 것보다 훨씬 더 많은 가스가 든다. 따라서 가능하면 저장소를 직접 사용하는 것을 피하는 것이 좋다. 예를 들어, 일부 계산 중에 저장소 변수를 여러 번 읽어야 하는 경우, 먼저 그 값을 메모리 변수에 복사하는 것이 좋다. 이렇게 하면 매번 저장소에 접근하는 대신 더 저렴한 메모리 변수에 반복적으로 접근하여 가스 비용을 절약할 수 있다.

> **참고**
>
> 맥락을 파악하기 위해, 솔리디티는 데이터를 보관하는 여러 장소를 사용한다: 영구적이고 비싼 storage; 실행 중에 임시적이고 더 저렴한 memory; 주로 외부 함수 입력에 사용되는 읽기 전용 영역인 calldata; 그리고 매우 수명이 짧은 값에 사용되며 접근 비용이 가장 저렴한 stack. 올바른 것을 선택하는 것은 데이터가 어떻게 사용되는지, 지속되어야 하는지, 변경 가능한지, 외부에서 전달되는지에 따라 달라진다. 14장에서 이러한 구분에 대해 더 깊이 다루겠지만, 성능과 비용에 어떤 영향을 미치는지 일찍 생각하기 시작하는 것이 도움이 된다.

### 가스 비용 추정하기

이더리움이 처음 출시되었을 때, 가스 비용을 추정하는 것은 경매에서 낙찰가를 맞추려는 것과 비슷했다. 비트코인이 트랜잭션 수수료를 처리하는 방식과 유사했다: 자체 가스 가격을 설정하고 채굴자는 가장 높은 입찰가를 가진 트랜잭션을 우선시했다. 이것은 바쁜 시간에 트랜잭션이 빠르게 처리되도록 하기 위해 종종 더 많이 제안해야 한다는 것을 의미했다. 효과가 있었지만, 네트워크가 혼잡할 때 가스 가격이 어디서든지 나타날 수 있음을 의미했다—때로는 하늘 높이 치솟았다.

그런 다음 2021년에 게임을 바꾼 EIP-1559가 도입되었다. 올바른 가스 가격을 추측하는 대신, 이더리움은 네트워크 활동에 따라 자동으로 조정되는 기본 수수료를 도입했다. 이것은 가스 수수료를 훨씬 더 예측 가능하게 만든다. 또한, 급한 경우 속도를 높이기 위해 팁(*우선 수수료*라고 함)을 추가할 수 있다. 이제 가스 비용을 추정하는 것이 더 간단해졌고, 트랜잭션을 처리하기 위해 과다 지불할 가능성이 줄어들었다.

트랜잭션의 가스 비용을 추정하는 방법을 자세히 살펴보자. 먼저, 모든 트랜잭션은 가스 비용의 두 가지 주요 구성 요소를 가지고 있다: 기본 수수료와 우선 수수료:

**기본 수수료(Base fee)**

이것은 트랜잭션이 블록에 포함되기 위해 지불해야 하는 최소 가스량이다. 기본 수수료는 네트워크에 의해 자동으로 결정되며 네트워크가 얼마나 바쁜지에 따라 동적으로 조정된다. 블록이 가득 차면 기본 수수료가 올라가고; 블록이 덜 사용되면 내려간다.

**우선 수수료(Priority fee, 팁)**

이것은 채굴자(또는 PoS 컨텍스트에서 검증자)가 트랜잭션을 우선시하도록 장려하기 위해 추가하는 추가 수수료이다. 트랜잭션이 더 빨리 처리되도록 하기 위해 주는 팁과 같다. 이 수수료는 직접 설정할 수 있지만, 지갑 애플리케이션은 원하는 트랜잭션 포함 속도에 따라 적절한 값을 제안한다.

이제 트랜잭션의 총 가스 비용은 사용된 가스(트랜잭션의 복잡성에 따라 달라짐)에 유효 가스 가격을 곱하여 계산된다. 유효 가스 가격은 기본 수수료와 우선 수수료의 합이다.

따라서 가스 비용을 추정하려면 다음 단계를 따른다:

1. [Etherscan Gas Tracker](https://oreil.ly/ZIqcq)와 같은 가스 추적 도구나 Web3 라이브러리(예: [ethers의 `maxFeePerGas()`](https://oreil.ly/YuBC0))를 통해 찾을 수 있는 현재 기본 수수료를 확인한다.
2. 트랜잭션이 얼마나 빨리 처리되기를 원하는지에 따라 우선 수수료(또는 팁)를 선택한다. 급한 경우 팁을 올릴 수 있다. 가스 추적 도구는 현재 네트워크 상태와 얼마나 빨리 처리해야 하는지에 따라 적절한 팁 금액을 파악하는 데 도움이 된다.
3. 총 가스 가격(기본 수수료 + 팁)에 예상 가스 사용량을 곱한다. 개발자라면 Web3 라이브러리(예: [ethers의 `estimateGas()`](https://oreil.ly/sfqZP))를 사용하여 이 예상 가스 사용량을 계산할 수 있다. 하지만 일반 사용자라면 걱정할 필요 없다—트랜잭션을 보낼 때 모든 지갑 앱이 자동으로 처리해 준다.

예를 들어, 기본 수수료가 20 gwei이고, 팁을 2 gwei로 설정하고, 트랜잭션이 50,000 가스를 사용하면, 예상 가스 비용은:

(20 gwei + 2 gwei) × 50,000 = 1,100,000 gwei

따라서, 110만 gwei, 또는 0.0011 ETH이다.

메인넷에 컨트랙트를 배포할 때 놀라지 않도록 개발 워크플로의 일부로 함수의 가스 비용을 평가하는 것이 권장된다.

## 결론

이 장에서는 스마트 컨트랙트를 자세히 다루기 시작했고 솔리디티 컨트랙트 프로그래밍 언어를 탐구했다. 간단한 예제 컨트랙트 *Faucet.sol*을 가져와 점차 개선하고 더 복잡하게 만들어 솔리디티 언어의 다양한 측면을 탐구하는 데 사용했다. 8장에서는 또 다른 컨트랙트 지향 프로그래밍 언어인 바이퍼로 작업할 것이다. 바이퍼를 솔리디티와 비교하여 이 두 언어 설계의 일부 차이점을 보여주고 스마트 컨트랙트 프로그래밍에 대한 이해를 심화시킬 것이다.
