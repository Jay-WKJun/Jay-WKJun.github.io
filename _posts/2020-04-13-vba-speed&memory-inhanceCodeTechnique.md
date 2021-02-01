---
layout: post
title: vba code technique
tags: [VBA]
excerpt_separator: <!--more-->
---

# inhance vb code

여기서는 vb의 성능 개선을 위한 내용을 다룹니다.

<!--more-->

# #1

엑셀의 모든 셋팅 값들을 FALSE처리하세요.

```vb
screenUpdateState = Application.ScreenUpdating
statusBarState = Application.DisplayStatusBar
calcState = Application.Calculation
eventsState = Application.EnableEvents
displayPageBreakState = ActiveSheet.DisplayPageBreaks
 
Application.ScreenUpdating = False
Application.DisplayStatusBar = False
Application.Calculation = xlCalculationManual
Application.EnableEvents = False
ActiveSheet.DisplayPageBreaks = False ‘note this is a sheet-level setting
```
 
‘>>개발 코드 부분<<
 
```vb
Application.ScreenUpdating = screenUpdateState
Application.DisplayStatusBar = statusBarState
Application.Calculation = calcState
Application.EnableEvents = eventsState
ActiveSheet.DisplayPageBreaks = displayPageBreaksState
```

​ScreenUpdating은 작업이 이루어 지는 동안 계속 화면이 그려질지에 대한 셋팅 값입니다. False 처리를 하면 그려지지 않다가 작업 이후에 한 꺼번에 그려집니다.

DisplayStatusBar는 엑셀 아래 상태바에 값이 찍힐지 여부인데요. 이 부분은 전 크게 속도 향상은 느끼지는 못하는 것 같습니다.

Calculation 셀들의 계산이 작업하는 동안 계속 이뤄 질지에 대한 속성인데요. 시트가 많을 수록 레퍼런스를 많이 참조할 수록  이 부분을 FALSE 처리해 놓으면 속도 향상은 꽤 있습니다.

EnableEvents 이건 아시다시피 이벤트 실행 여부 입니다. 이것도 딸려 있는 이벤트들이 많다면 False 처리가 큰 도움이 됩니다.

DisplayPageBreaks 페이지 나누기 기능을 끄는 것인데.. 전 잘 사용하지 않으나 속도 개선에 도움이 된다고 합니다.

특별히 프로세싱중 화면에 보여줘야 하는 것이 아니라면 상태바에 현재 진행 상태를 표시하기위해 DisplayStatusBar만 True로 해 놓고 나머지 는 위 소스를 그대로 쓰시고 사용하시면 속도 향상에 도움을 보실거에요.

# #2

두번 째, 대규모 단위의 범위를 LOOP를 돌려서 값을 변경 시키는 경우는

바로 값을 꼽기 보다는 변수로 해당 범위를 받아서 메모리에 올려 값을 변경한 후에 Range의 값을 한 번에

변경하세요.

아래와 같이 말이죠.

[느린 소스]

```vb
    Dim DataRange as Range
    Dim Irow as Long
    Dim Icol as Integer
    Dim MyVar as Double
    Set DataRange=Range(“A1:C10000”)
 
    For Irow=1 to 10000
      For icol=1 to 3
        MyVar=DataRange(Irow,Icol)  ‘값을 읽어올 때 속도 지연
        If MyVar > 0 then
          MyVar=MyVar*Myvar ‘ Change the value
          DataRange(Irow,Icol)=MyVar  ‘값을 쓸 때 속도 지연
        End If
      Next Icol
    Next Irow
```
 
[빠른 소스]

```vb
    Dim DataRange As Variant
    Dim Irow As Long
    Dim Icol As Integer
    Dim MyVar As Double
    DataRange = Range(“A1:C10000”).Value ‘ 여기서 한번에 읽어서 메모리로 올립니다.
 
    For Irow = 1 To 10000
      For Icol = 1 To 3
      MyVar = DataRange(Irow, Icol)
      If MyVar > 0 Then
        MyVar=MyVar*Myvar ‘ Change the values in the array
        DataRange(Irow, Icol) = MyVar
      End If
    Next Icol
    Next Irow
    Range(“A1:C10000”).Value = DataRange ‘여기서 결과 값을 셀로 연결합니다.
```


데이터가 많지 않은 경우에는 큰 효과를 보지는 못합니다.

작업을 많이 하시다 보면 아시게 되지만,

프로그램이 도는 동안 화면(셀)에 어떠한 작업을 계속 한다는 건 속도 저하에 대단히 큰 영향을 주게 됩니다.

# #3

세번 째, 다수의 Shape(도형)에 작업을 하는 경우에는 코드상에서 직접 Select를 하지 말아주세요.

아래와 같이 말이죠.

```vb
    For i = 0 To ActiveSheet.Shapes.Count
       ActiveSheet.Shapes(i).Select
       Selection.Text = “Hello”
    Next i
```
 
그렇다면 어떻게??

```vb
    For i = 0 To ActiveSheet.Shapes.Count
       ActiveSheet.Shapes(i).TextEffect.Text = “Hello”
    Next i
```
 
저같은 경우에는 이렇게 전체 Shape를 변경하는 일이 없어서..써보지는 않았습니다.

한 두개씩 변경을 하는데.. 첫 번째 소스같이 구지... Select는 하지 않죠.

# #4

네번째, 피벗 테이블도 업데이트 되지 않게 묶어 놓으세요.

피벗 테이블의 이름을 알아야 겠죠.

```vb
ActiveSheet.PivotTables(“PivotTable1”).ManualUpdate=True
‘개발 코드 부분
ActiveSheet.PivotTables(“PivotTable1”).ManualUpdate=False
```

# #5
 
다섯 번째, 가급적 With 구분을 활용하세요.

Select하고 Selection.XXX 해서 사용하지 말고. With문으로 쓰라는 건데.. 결과적으로 Select하지 말라는 겁니다.

[With 구문 사용 전]

```vb
 Range(“A1”).Select
Selection.Font.Bold = True
Selection.Font.Italic = True
Selection.Font.Underline = xlUnderlineStyleSingle
```

[With 구문 사용]

```vb
With Range(“A1”).Font
    .Bold = True
    .Italic = True
    .Underline = xlUnderlineStyleSingle
End With
```
​ 
# #6

여섯 번째, 공백 문자를 넣을 때는 "" 대신에 vbNullString을 쓰세요.

```vb
tmpString = ""

tmpString = vbNullString
```

# #7

일곱 번째, 가급적 선언되어진 객체들은 메모리 해제를 시켜주세요.

```vb
Set tmpObject = Nothing
```

# #8

여덟번째, 소스는 가급적 한줄로 쓸 수 있다면 그렇게 해 주세요.

[SLOW]

```vb
    With Selection
        .WrapText = True
        .ShrinkToFit = False
    End With
```

[FAST]

```vb
    With Selection
        .WrapText = True: .ShrinkToFit = False
    End With
```

근데.. 이건.. 코드 가독성 때문에 전 가급적 두줄로 변경해서 쓰긴 합니다.

퍼포먼스에 큰 영향을 주지 않아서도 그렇구요..  하지만 조금의 차이는 발생하니 참고로 알아 두세요.

# #9

아홉번째, 불필요한 셀 복사 붙여 넣기는 피해주세요.

```vb
Sheet1.Range("A1:A200").Copy
Sheet2.Range("B1").PasteSpecial
Application.CutCopyMode = False  

Sheet1.Range("A1:A200").Copy Destination:= Sheet2.Range("B1")
```

```vb
Sheet1.Range("A1:A200").Copy
Sheet2.Range("B1").PasteSpecial xlPasteValues
Application.CutCopyMode=False

Sheet2.Range("B1:B200").Value = Sheet1.Range("A1:A200").Value
```

```vb
heet1.Range("A1:A200").Copy
Sheet2.Range("B1").PasteSpecial xlPasteFormulas
Application.CutCopyMode=False

Sheet2.Range("B1:B200").Formula = Sheet1.Range("A1:A200").Formula
```

# #10

열번째, 가급적 엑셀에서 제공해주는 함수를 사용하세요. 같은 기능이라면 직접 만든 로직보다 속도가 훨씬 좋습니다.

저도 처음에는 다른 언어와 같이 대부분 함수를 만들어서 사용했었는데요.

기능이 같은 경우, 엑셀에서 제공해주는 함수를 썼을 때의 속도가 훨씬 빨랐습니다

# #11

열한번째, For 구문 보다는 For Each 구문을 활용하세요.

쭉... 여러가지를 나열해 보았습니다.

첫번째 말씀드린 엑셀 셋팅값 처리는 개발 시 무조건 해주어야 하고,

나머지 것들은 퍼포먼스 결과를 보면서 필요시에 사용해 주시면 됩니다. 각자 개발 스타일들이 있기 때문에

꼭 모두 적용할 필요는 없다고 봅니다.