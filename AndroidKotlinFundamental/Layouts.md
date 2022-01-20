# Layouts

## View Groups
+ ViewGroup은 하위 view 또는 viewGroup을 포함하고 있는 view
+ LinearLayout은 하위 view를 수직 또는 수평으로 정렬할 수 있다.
	```xml
	android:orientation="vertical" // 수직 정렬
	android:orientation="horizontal" // 수평 정렬
	```
   
## Layout Editor
+ xml파일을 android에 내장되어 있는 editor를 이용해 작성할 수 있다.
	+ Palette : view 및 viewgroup의 목록
	+ Attributes : 현재 선택한 view의 특성을 설정할 수 있다.
	+ Component Tree : view계층을 표시 크기가 작거나 숨겨진, 겹쳐진 view를 선택 및 추가하는데 유용하다.
   
## Style
+ style은 view의 모양 및 속성의 모음으로 resource로 만들어 관리할 수 있다.
+ view의 기능(?)에 따라 style을 재사용해서 앱의 일관성을 유지할 수 있다.

## ScrollView
+ scrollview는 viewGroup으로 오직 하나의 하위 view 또는 viewGroup을 포함할 수 있다.
+ 여러 view를 scrollview에 넣고 싶다면 LinearLayout에 포함시키는 방법이 있음