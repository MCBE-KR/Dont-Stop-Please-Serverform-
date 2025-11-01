# Dont-Stop-Please-Serverform-
서버폼 렉을 개선하기 위한 방법을 찾고 기록하는 프로젝트

### 서버폼이 렉걸리는 이유
- binding에서 visible을 끄는건 보이지 않게 하는거지 렌더링을 안하는게 아님
- 폼 종류가 많아질수록 안보이지만 렌더링되는 팩토리가 많아진다.
- 팩토리가 많아지면 사실상 버튼이 N승 단위로 많아지는 것이나 마찬가지이기 때문에 버튼 수에 따른 렉이 커진다

### 왜 visible을 건드리냐 ignored를 쓰면 되는거 아니냐
- 서버폼 관련 값은 모두 바인딩값 #로만 들어옴
- 하지만 ignored는 바인딩을 사용 못함
- requires도 마찬가지
- 그리고 bindings는 visible밖에 지원을 안함

### 서버폼 렉 줄이기의 핵심은 안쓰는 패널을 지우는 것
- 아무리 요소를 줄이고 관리를 잘 해봤자 폼 종류가 늘어나면 무조건 렉이 걸릴 수 밖에 없음
- 그래서 최대한 필요없는 렌더링을 지워야함
- ignore requires가 작동하면 직빵으로 해결인데 모장이 해줄리가 없죠~

## 최적화 솔루션
- [low_frequency_rendering](solutions/low_frequency_rendering.md)
- [clip_children으로 불필요한 하위 요소 잘라내기](solutions/clip_children.md)
- [stack_panel를 이용하여 패널 삭제하기](solutions/delete_by_stack_panel.md)
- [버튼 패널 하나로 모든 것을 처리하기](solutions/everything_by_onebtn.md)
- 뭔가 더 있었는데 아오...

## 연구중
- stack_panel에서 종류별 폼을 화면 전체 사이즈로 넣어서 보여줄 폼 하나가 로드되면 나머지 패널들을 화면 밖으로 밀어내서 clip_children으로 삭제하기

<b>이 내용은 bedrock addons와 bedrock wiki에서 가져온 것이나 따로 직접 연구된 것들이며 절대 완벽하지 않습니다</b>

<b>이 설명들은 모두 최적화를 위한 것들이기 때문에 서버폼 자체에 대한 지식은 제공하지 않습니다</b>

<b>저도 뭐가 더 좋은지 그런거 모릅니다. 그냥 방법들을 나열한거고 어디서 어떻게 활용할지는 각자 또 연구해봐야죠</b>