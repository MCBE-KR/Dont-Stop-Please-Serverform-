# grid 마커 기법
[◀](../README.md)

[performance_panel](performance_panel.md) 안에 넣은 패널에서는 stack_panel로 짠 버튼 목록이 사라진다.
버튼 텍스트, 텍스쳐, `#visible` 같은 **컬렉션 바인딩은 grid 안에서만 제대로 평가되기 때문.**
stack_panel이나 factory 슬롯에서는 안 된다.

그래서 버튼은 grid를 조작해서 배치한다.
이게 사라지는 현상도 해결하고 최적화에도 꽤 효과가 있다.

### stack_panel + factory 대신 grid
보통 버튼 하나를 원하는 자리에 놓으려고 이런 걸 많이 쓴다.
```json
"global_dynamic": {
    "type": "stack_panel",
    "orientation": "horizontal",
    "factory": {
        "name": "buttons",
        "control_name": "$control"
    },
    "collection_name": "form_buttons",
    "bindings": [
        {
            "binding_name": "#form_button_length",
            "binding_name_override": "#collection_length"
        }
    ]
}
```
이걸 grid로 바꾼다.
```json
"global_grid_in": {
    "type": "panel",
    "size": [0, 0],
    "controls": [
        { "panel@$control": {} }
    ]
},

"global_grid": {
    "type": "grid",
    "grid_dimensions": [100, 1],
    "grid_item_template": "test.global_grid_in",
    "collection_name": "form_buttons"
}
```
- 셀 크기가 [0, 0]이라 모든 버튼이 한 점에 겹친다.
- 셀 안의 `$control`은 버튼 텍스트를 보고 자기 버튼일 때만 보인다.
```json
{
    "binding_type": "view",
    "source_property_name": "(#text = 'exit')",
    "target_property_name": "#visible"
}
```
- 쓸 때는 `"close@test.global_grid": { "$control": "test.exit_button", "anchor_from": "top_right", ... }` 이런 식으로 위치만 잡아주면 된다.
- stack_panel + factory 비용이 빠지고, 컬렉션 바인딩도 grid 안이라 제대로 들어온다.

### 버튼 하나당 grid 하나
- 서버폼의 `form_buttons` grid는 무조건 0번 버튼부터 채운다.
- 그러면 i번 버튼 하나만 원하는 자리에 놓고 싶을 때는?
- 높이가 `i+1`칸인 grid를 만들고 위로 i칸 밀어 올린다.
- 위로 밀려난 0 ~ i-1번 행은 [clip_children](clip_children.md)으로 잘라버린다.

#### 예시 (3번 버튼을 원하는 자리에)
```json
"slot_3": {
    "type": "panel",
    "size": [200, 60],
    "clips_children": true,
    "controls": [
        {
            "grid": {
                "type": "grid",
                "collection_name": "form_buttons",
                "grid_dimensions": [1, 4],
                "grid_item_template": "test.cell",
                "anchor_from": "top_left",
                "anchor_to": "top_left",
                "size": ["100%", "400%"],
                "offset": [0, "-300%"]
            }
        }
    ]
}
```
- `grid_dimensions`가 [1, 4]라서 0 ~ 3번 버튼 4줄이 만들어진다.
- `size`를 400%로 줘서 한 줄이 자리 크기랑 같게 만든다.
- `offset`으로 3줄 위로 올리면 3번 버튼이 딱 자리에 온다.
- 나머지 위쪽 3줄은 잘려서 안 보임

### 마커로 엉뚱한 버튼 막기
- 버튼 텍스트 앞에 안보이는 색 코드 조합(`§g§u§a§r` 같은거)이 마커로 붙어 들어온다고 치자.
- 슬롯은 자기 마커가 있을 때만 그린다.
```json
"marked": {
    "type": "panel",
    "size": ["100%", "100%"],
    "$prefix|default": "§z§z§z",
    "bindings": [
        {
            "binding_name": "#form_button_text",
            "binding_name_override": "#text",
            "binding_type": "collection",
            "binding_collection_name": "form_buttons"
        },
        {
            "binding_type": "view",
            "source_property_name": "(not ((#text - $prefix) = #text))",
            "target_property_name": "#visible"
        }
    ]
}
```
상황따라 버튼이 빠져서 순서가 밀려도 다른 버튼이 그 자리에 그려지지 않는다.

### 이게 왜 최적화냐
- [버튼 패널 하나로 모든 것을 처리하기](everything_by_onebtn.md)에서 말한 거의 그 구조다.
- 팩토리 여러개 대신 grid 셀 하나에 버튼 패널 하나만 쓰고, 마커로 자리를 정한다.
- 화면 밖으로 밀려난 줄은 clip_children으로 잘리니까 그릴 게 줄어든다.

### 주의할 점
- i번 슬롯은 0 ~ i번 줄을 다 만든다. 잘려서 안 보일 뿐이다.
- 그래서 버튼 번호가 커질수록 셀이 많아진다. 한 화면에 쓰는 슬롯 수를 적게 잡는 게 좋다.
- 슬롯이 많아지면 손으로 쓰다간 미친다. 스크립트로 json을 뽑는 걸 추천
