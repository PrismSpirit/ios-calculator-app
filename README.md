## ➕➖계산기✖️➗

### 목차
[1. 소개](#1-소개)  
[2. 팀원](#2-팀원)  
[3. 타임라인](#3-타임라인)  
[4. 프로젝트 구조](#4-프로젝트-구조)  
[5. 실행 화면](#5-실행-화면)  
[6. 트러블슈팅](#6-트러블슈팅)  
[7. 참고 자료](#8-참고-자료)  

---
### 1. 소개
#### **➕➖계산기✖️➗**
간단한 사칙연산을 수행하는 계산기입니다. 계산할 때마다 계산 내역을 기록해 어떤 계산을 했는 지 스크롤 뷰를 조작해 확인할 수 있습니다. 🔎

### 2. 팀원
| <img src="https://avatars.githubusercontent.com/u/27756800?s=48&v=4" width="200"> |
| :---: |
| Prism ([Github](https://github.com/PrismSpirit/)) |

### 3. 타임라인
| 날짜 | 내용 |
| --- | --- |
| 24.02.05(월) ~ 02.09(금) | **Operand와 Operator를 담아둘 Queue와 Queue에서 사용할 List 구현**<br>- Queue에 데이터를 저장하는 자료구조로 Doubly Linked List를 사용 |
| 24.02.12(수) ~ 02.15(목) | **Class Diagram 작성 및 세부 구현**<br>- Class Diagram을 따르도록 Type과 Type의 프로퍼티, 메서드 구현 |
| 24.02.16(금) ~ 02.20(화) | **계산기 UI 구현**<br>- 사용자로부터 입력을 받아 실제로 계산을 수행할 수 있도록 구현<br>- 사용자의 악의적인 입력에 대응하도록 예외처리 |
| 24.02.21(화) ~ 02.23(금) | **계산 내역을 기록하는 기능 추가**<br>- 사용자의 계산 내역을 기록해 스크롤 뷰로 보여주도록 구현<br>- 계산 내역이 상단 공간을 넘어 추가될 경우 최하단으로 자동으로 이동하도록 구현 |


### 4. 프로젝트 구조
#### Class Diagram
![img](<https://raw.githubusercontent.com/PrismSpirit/ios-calculator-app/ic_11_prism/Calculator_ClassDiagram.png>)

### 5. 실행 화면
| ![img](<https://raw.githubusercontent.com/kkomgi/ios-calculator-app/Team_step1/Screenshots/subtract.gif>) | ![img](<https://raw.githubusercontent.com/kkomgi/ios-calculator-app/Team_step1/Screenshots/ACButton.gif>) | ![img](<https://raw.githubusercontent.com/kkomgi/ios-calculator-app/Team_step1/Screenshots/CEButton.gif>) |
| :---: | :---: | :---: |
| 뺄셈 계산하기 | AC버튼 누르기 | CE버튼 누르기 |

| ![img](<https://raw.githubusercontent.com/kkomgi/ios-calculator-app/Team_step1/Screenshots/signToggleButton.gif>) | ![img](<https://raw.githubusercontent.com/kkomgi/ios-calculator-app/Team_step1/Screenshots/dotOnlyOnce.gif>) | ![img](<https://raw.githubusercontent.com/kkomgi/ios-calculator-app/Team_step1/Screenshots/operatorsInput.gif>) |
| :---: | :---: | :---: |
| 부호 변환하기 | 소수점은 한 번만 | 연산자 바꾸기 |

| ![img](<https://raw.githubusercontent.com/kkomgi/ios-calculator-app/Team_step1/Screenshots/divisionByZero.gif>) | ![img](<https://raw.githubusercontent.com/kkomgi/ios-calculator-app/Team_step1/Screenshots/automaticallyChangeDoubleToIntWhenFractionIs0.gif>) | ![img](<https://raw.githubusercontent.com/kkomgi/ios-calculator-app/Team_step1/Screenshots/automaticallyScrollDownToBottom.gif>) |
| :---: | :---: | :---: |
| 0으로 나누기 | 결과가 정수일 경우 소수점 생략 | 계산 입력 시 자동 스크롤 |

### 6. 트러블슈팅
#### 1) ❗️ 부호 전환 시 붙는 문자와 연산자 문자가 동일해 생기는 문제
##### 📌 문제 상황
```swift
// Operator.swift
enum Operator: Character, CaseIterable, CalculateItem {
    case add = "+"
    case subtract = "-"
    case divide = "/"
    case multiply = "*"
}
```
```swift
// ExpressionParserTests.swift
func test_Parse() {
    // Given
    let expression = "120.0/3.+-20-10.0*4.0/2.0"
    
    // When
    let resultFormula = ExpressionParser.parse(from: expression)
    
    // Then
    XCTAssertEqual(someFormula, resultFormula)
    }

```
부호 전환 시 Operand에 붙는 문자와 연산자 문자가 동일해 파싱이 의도한대로 동작하지 않고, 더하기 연산을 제외한 나머지 연산은 계산기UI 버튼의 문자와 다른 문제가 있었습니다.

##### 🛠️ 해결 방법
```swift
// Operator.swift
enum Operator: Character, CaseIterable, CalculateItem {
    case add = "+"
    case subtract = "−"
    case divide = "÷"
    case multiply = "×"
}
```
```swift
// ExpressionParser.swift
func test_Parse() {
    // Given
    let expression = "120.0÷3.+-20−10.0×4.0÷2.0"
    
    // When
    let resultFormula = ExpressionParser.parse(from: expression)
    
    // Then
    XCTAssertEqual(someFormula, resultFormula)
}
```
부호 전환시 Operand에 붙는 문자를 -(`U+002D`), 연산자 문자를 −(`U+2212`)로 구분해 주었고, Operator type의 subtract의 rawValue를 −(`U+2212`)로 지정해 파싱이 정상적으로 작동하도록 수정했습니다. 추가로, 곱하기와 나누기 연산자도 계산기UI 버튼의 title과 동일한 문자로 설정했습니다.

#### 2) ❗️ 계산 내역이 상단 공간을 넘어 추가될 때 이동이 비정상적으로 작동하는 문제
##### 📌 문제 상황
<img src="https://raw.githubusercontent.com/PrismSpirit/ios-calculator-app/ic_11_prism/Screenshots/withoutLayoutIfNeeded.gif" alt="drawing" width="200"/>

계산 내역이 상단 공간을 넘어 추가될 때 최하단으로 자동으로 이동해야 하나 그것보다 조금 덜 이동하는 문제가 있었습니다.

##### 🛠️ 해결 방법
<img src="https://raw.githubusercontent.com/PrismSpirit/ios-calculator-app/ic_11_prism/Screenshots/withLayoutIfNeeded.gif" alt="drawing" width="200"/>

`layoutIfNeeded()`로 scrollView의 subview인 logsStackView의 레이아웃을 즉시 업데이트 하도록 해주어 올바른 contentSize를 계산해 최하단으로 이동하도록 해주었습니다.

### 7. 참고 자료
📍[Generics](<https://docs.swift.org/swift-book/documentation/the-swift-programming-language/generics>)  
📍[Protocols](<https://docs.swift.org/swift-book/documentation/the-swift-programming-language/protocols>)  
📍[Testing your apps in Xcode](<https://developer.apple.com/documentation/xcode/testing-your-apps-in-xcode>)  
📍[View Programming Guide for iOS](<https://developer.apple.com/library/archive/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/WindowsandViews/WindowsandViews.html#//apple_ref/doc/uid/TP40009503-CH2-SW1>)  
📍[UIStackView](<https://developer.apple.com/documentation/uikit/uistackview>)  
📍[UIScrollView](<https://developer.apple.com/documentation/uikit/uiscrollview>)  
📍[layoutIfNeeded](<https://developer.apple.com/documentation/uikit/uiview/1622507-layoutifneeded>)  
