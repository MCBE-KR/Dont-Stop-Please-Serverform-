# collection_panel로 안쓰는 폼 패널 지우기 (performance_panel)
[◀](../README.md)

[stack_panel를 이용하여 패널 삭제하기](delete_by_stack_panel.md)랑 원리는 같다.
`#collection_length`가 비면 **내부 패널을 아예 안 만든다.**

다른 점은 조건식 결과로 property_bag 값을 꺼내 쓴다는 것.
그래서 조건식 하나로 "만들지 말지"랑 "어떤 패널을 만들지"를 같이 정할 수 있다.

#### 예시
```json
"performance_panel": {
    "type": "collection_panel",
    "size": ["100%", "100%"],
    "$condition|default": "",

    "property_bag": {
        "#collection_length_true": "$form_arr",
        "#collection_length_false": []
    },

    "bindings": [
        {
            "binding_name": "#title_text",
            "binding_type": "global"
        },
        {
            "binding_type": "view",
            "source_property_name": "(#title_text = $condition)",
            "target_property_name": "#condition"
        },
        {
            "binding_type": "view",
            "source_property_name": "('#collection_length_' + #condition)",
            "target_property_name": "#collection_length"
        }
    ],

    "factory": {
        "name": "$condition",
        "control_ids": {
            "menu": "@test.menu_panel",
            "shop": "@test.shop_panel",
            "status": "@test.status_panel"
        }
    }
}
```
그리고 서버폼 화면 안에 폼 종류마다 하나씩 깔아둔다.
```json
"initial": {
    "type": "panel",
    "size": ["100%", "100%"],
    "controls": [
        {
            "menu@test.performance_panel": {
                "$form_arr": ["menu"],
                "$condition": "ll<menu"
            }
        },
        {
            "shop@test.performance_panel": {
                "$form_arr": ["shop"],
                "$condition": "ll<shop"
            }
        }
    ]
}
```

### 어떻게 돌아가는거냐
- `(#title_text = $condition)`은 `true` 아니면 `false`다.
- 이걸 문자열 뒤에 붙이면 `'#collection_length_true'` 아니면 `'#collection_length_false'`가 된다.
- 그 이름으로 property_bag 값을 꺼내서 `#collection_length`에 넣는다.
- 제목이 맞으면 `["menu"]`가 들어가고 factory가 `control_ids`의 menu 패널 **하나만** 만든다.
- 제목이 다르면 `[]`가 들어가고 **아무것도 안 만든다.** 있던 것도 지워진다.

ignored랑 requires는 바인딩을 못 쓰지만 `#collection_length`는 바인딩으로 바꿀 수 있다.
그러니까 사실상 바인딩으로 쓰는 ignored나 마찬가지.

### 좋은 점
- 폼이 90종이 넘어도 실제로 만들어지는 패널은 열린 폼 하나뿐이다.
- 나머지 collection_panel은 제목 비교 한번 하고 끝난다.
- `control_ids`는 다같이 같은 목록을 써도 된다. 뭘 만들지는 `$form_arr`에 적은 이름이 고른다.

### 주의할 점
- `$condition`은 폼 제목이랑 **완전히 같아야** 한다. `=` 비교라서 `ll<guild`랑 `ll<guildwar`가 안 겹친다.
  - 접두사 검사(`((#title_text - 'll<') = #title_text)`)로 바꾸면 겹치니까 조심
- 이 안에 들어간 패널에서는 **stack_panel로 짠 버튼 목록이 사라진다.** 버튼 컬렉션 바인딩이 제대로 안 들어온다.
  - 그래서 버튼 배치는 grid로 해야한다 → [grid로 버튼 배치하기](grid_placement.md)
- 폼을 하나 추가할 때마다 `_ui_defs.json`, factory의 `control_ids`, 깔아두는 목록 세군데를 같이 고쳐야 함
