# 메이드 아가씨 스크립팅 방법


이 문서는 메이드 아가씨의 스크립팅 문법을 설명합니다.


# 기반 언어

메이드 아가씨는 I_Jemin 이 자체 제작한 키리노 엔진 (Kirino Engine on Unity) 을 사용합니다.

키리노 엔진의 문법은 렌파이 문법에서 파생되었습니다. 키리노 엔진의 문법은 렌파이 엔진 문법을 간략화한 버전입니다.

키리노 엔진 문법의 기초적인 문법 대부분은 렌파이의 기초 문법과 호환됩니다. 


# 기본 서식

## 파일 확장자

키리노 엔진에서 사용하는 스크립트 파일은 .rpy 확장자를 가져야 합니다.

## 들여쓰기

키리노 엔진은 들여쓰기를 통해 종속관계를 정의합니다. 이때 들여쓰기는 반드시 tab 이 아니라 4space (스페이스 공백 네개) 를 사용해야 합니다.

대부분의 코드 에디터는 .rpy 확장자를 인식하여 tab 키를 사용하면 자동으로 공백4번으로 입력됩니다. 예외가 있을 수 있으니 에디터의 설정을 확인해주세요.


# 기본 문법

## 주석

주석은 키리노 엔진이 처리하지 않아 메모로 사용할수 있습니다.

주석을 사용하는 방법은 아래와 같습니다.

~~~
# 이것은 컴퓨터가 처리하지 않습니다.
~~~

## 레이블 label

레이블은 하나의 개별 스토리를 묶는 단위 입니다. 또한 많은 대사나 문단이 있는 덩어리를 쪼개기 위해 사용할 수 있습니다.

정확히 얘기하면 전체 스토리는 개별 스토리의 조합이고, 개별 스토리는 여러 대사로 이루어진 한 장면을 의미합니다.
쉽게 말해서 레이블은 대사와 연출 덩어리를 묶는 단위입니다.

배경이 바뀌고 이야기의 흐름이 전환되면 다른 장면이 된것입니다. 따라서 그 다음 대사들은 직전의 내용과는 다른 레이블로 묶어주면서 적절히 조직화 하는 것을 추천합니다.

레이블을 사용하는 방법은 다음과 같습니다.

~~~
label 장면이름:
    # 해당 장면의 대사와 스크립트들

~~~

장면 이름은 가능한 영어로 표기해주세요.<br>또한 어떤 레이블에 속하는 대사와 연출들은 해당 레이블 밑에서 들여쓰기로 작성해주세요.

예시
~~~
label lee-story-begin:

    "이준영" "난 경기도 안양의 이준영이다"
    "이준영" "존나 쎄지"

    play "gangsta-rap"

label lee-story-ending:

    "이준영" "그래서 나는 세상을 제패했다"

    return
~~~

일반적으로 곧바로 이어져 있는 두 레이블은, 상위 레이블의 마지막 라인에 도달한 경우, 직후 이어서 그 다음 레이블의 스토리가 진행됩니다.

## 씬 scene

scene 키워드를 통해 배경화면을 바꿀 수 있습니다. 배경화면이 바뀔때 자동으로 페이드 인 아웃이 적용됩니다.

scene 키워드 다음에는 바꾸고자 하는 배경 이미지의 이름을 작성합니다.
배경 이미지를 미리 정의하는 것은 유니티 에디터 상에서 지정해야 합니다.

예시
~~~
label example-story:
    
    scene "morning-sky"

    "나" "아침 해가 밝았다"

    scene "rain-sky"

    "나" "그런데 얼마 지나지 않아 비가 내리기 시작했다"

    return

~~~

## 점프 jump

jump 키워드를 통해 이전까지의 진행을 모두 무시하고 원하는 레이블로 곧바로 스크립트 진행을 점프할 수 있습니다.

예시
~~~
# 아래의 스크립트는 story-B 를 무시하고 story-A 에서 story-C 로 바로 진행됩니다.

label story-A:

    "이것은 스토리 A 입니다"

    jump story-C

label story-B:

    "이것은 스토리 B 입니다"

label story-C:

    "이것은 스토리 C 입니다"

~~~

call 키워드와 달리 이동하기 직전의 마지막 호출 라인을 기억하지 않습니다. 따라서 jump 를 통해 어떤 레이블로 이동한 경우, return 을 통해 마지막 호출 지점으로 다시 돌아갈 수 없습니다.

스토리가 선형으로 진행되는 경우 call 보다 jump 를 사용하는 것이 이해하기 쉽고 일반적으로 자주 사용됩니다.

## 콜 call

call 키워드를 통해 원하는 레이블로 이동할 수 있습니다. 단 jump 와 다른 점은 이동한 레이블의 스토리가 마지막 라인에 도달하면, call 을 사용한 마지막 지점을 기억하고 있다가 되돌아가서 마지막 지점에서 부터 스토리를 계속 진행합니다.

call 을 사용한 경우 레이블은 호출한 순서대로 스택에 레이블 포인터가 쌓이게 됩니다. 그리고 call 한 레이블이 return 을 만나 스토리가 끝날때 마다 호출한 순서와 반대인 순서로서, 가장 최근에 호출된 지점으로 되돌아갑니다.

아래의 예를 보겠습니다.


~~~
label A:

    "이것은 A 레이블의 전반부 입니다"

    call B

    "이것은 A 레이블의 후반부 입니다"

    return

label B:

    "이것은 B 레이블의 전반부 입니다"

    call C

    "이것은 B 레이블의 후반부 입니다"

    return

label C:

    "이것은 C 레이블 입니다"

    return

~~~

위 스크립트의 대사는 아래와 같은 순서로 출력됩니다.

- "이것은 A 레이블의 전반부 입니다"
- "이것은 B 레이블의 전반부 입니다"
- "이것은 C 레이블 입니다"
- "이것은 B 레이블의 후반부 입니다"
- "이것은 A 레이블의 후반부 입니다" 

즉 C 레이블로 이동한 다음, C 레이블이 return 을 만나 끝날때 label B 에서 label C 로 이동하려고 했던 마지막 지점인 call C 라인으로 돌아가서 그곳에서 부터 계속 스크립트가 진행된 것 입니다.

루프물을 작성할때 jump 대신 call 을 사용한다면 코드를 효과적으로 간결하게 작성할 수 있습니다.

## 리턴 return

return 키워드는 일반적으로 스토리가 모두 진행되어, 현재 레이블의 스토리가 시작되기 전 지점으로 되돌아가야 할때 사용합니다.

return 키워드는 현재 레이블을 호출한 마지막 지점으로 돌아가게 합니다. 만약 돌아갈 지점이 없다면 현재 스크립트 진행을 종료합니다. 종료된 경우, 스크립트의 처음 시작위치나 혹은 유니티 C# 을 통해 지정한 대기상태로 가게 됩니다.

return 을 사용하는 방법은 다음과 같습니다.

~~~

label story:
    "나는 안양의 이준영이다"

    "그리고 여기서 나의 이야기는 끝이 난다"

    return

~~~

## 쇼 show

show 키워드를 통해 이미지를 출력할 수 있습니다. show 를 통해 출력할때, 만약 같은 태그를 공유하는 이미지가 이미 출력된 상태라면 해당 이미지를 지우고 show 로 지정한 이미지를 출력하면서 교체하는 방식으로 동작합니다. 이를 통해 같은 캐릭터의 여러 이미지가 쌍둥이 처럼 뜨는 경우를 방지할 수 있습니다.

이미지의 식별자와 태그는 유니티 에디터를 통해 지정해야 합니다.

show 를 사용하는 방법은 다음과 같습니다.

~~~
label story:
    show "gangsta_idle"
    "나는 갱스터다"

    show "gangsta_angry"
    "그리고 나는 매우 화가났다"

~~~

## 대사 출력

대사는 따옴표를 통해 출력할 수 있습니다.

들여쓰기는 하지 않아도 상관 없지만, 되도록 대사는 들여쓰기를 통해 어떠한 레이블 밑에 소속되게 하는 것을 추천합니다.

~~~
# 이렇게 해도 동작은 합니다.
"나는 갱스터다"

# 하지만 눈으로 쉽게 읽을 수 있도록 소속되는 레이블(이야기 묶음) 밑에 들여쓰기를 해주세요.
label real-gang:
    "나는 진정한 갱스터다"
~~~

만약 이름을 함께 출력하고 싶다면, 대사 앞에 이름을 따옴표로 묶어 작성하면 됩니다.

~~~
label real-gang:
    "이준영" "나는 진정한 갱스터다"
~~~

## 오디오 재생 play

일반적인 오디오는 play 키워드를 사용하여 일회 재생할 수 있습니다.

~~~
label real-gang:
    
    "이준영" "나는 진정한 갱스터라서 총을 쏜다 "

    # play 키워드 다음에 재생할 음악 파일의 이름을 따옴표로 묶어 작성합니다. 확장자는 뺍니다.
    play "gun-fire"

~~~

## 음악 재생 music

배경 음악은 music 키워드를 사용하여 재생할 수 있습니다. play 키워드와 달리 오디오가 반복 재생됩니다.

~~~
label real-gang:
    
    # music 키워드 다음에 재생할 음악 파일의 이름을 따옴표로 묶어 작성합니다. 확장자는 뺍니다.
    music "gangsta-rap"

    "이준영" "나는 진정한 갱스터다"
~~~

## 선택지 menu

menu 키워드를 사용하여 선택지를 만들 수 있습니다. 각각의 선택지를 나열한 다음, 선택지를 선택했을때 진행될 사항을 들여쓰기를 통해 해당 선택지 하단에 작성해주세요.

menu 키워드는 아래와 같이 사용합니다.

~~~

label event_2:
    show "front_good"

    아이리 "주인님, 주인님!"
    아이리 "오늘 처음으로 요리를 만들어 봤어요! 한번 드셔보세요!"

    "(그러고 보니 요리를 배우고 있댔나.)"
    "(먹어보자…….)"

    show "front_confuse"

    아이리 "어, 어떤가요, 주인님?!"

    menu:
        "맛있어.":
            show "side_happy"
            아이리 "아…… 다행이다아……."

            show "front_good"
            아이리 "네? 손가락에 감은 붕대요? "

            show "front_smile0"
            아이리 "시, 신경쓰지 마세요. 아무것도 아니니까요, 에헤헤."
            아이리 "맛있게 드셔주셔서 기뻐요, 주인님!"

        "직접 먹여줘.":
            show "front_shock"
            아이리 "주, 주인님도 참!"
            아이리 "지, 진심이신가요……? 알겠어요."
            아이리 "아, 아앙~"

            show "front_smile0"
            아이리 "에헤헤…… 왠지 좀 부끄럽네요."
            아이리 "맛은…… 아무래도 상관없다구요? 그건 곤란해요~"

        "조금 짜다.":
            jump event_2_3

~~~