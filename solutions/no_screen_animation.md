# 서버폼 화면 애니메이션 끄기
[◀](../README.md)

서버폼 화면은 기본으로 열고 닫을 때 화면 애니메이션이 붙어있다.
폼을 자주 열고 닫는 서버라면 이것도 다 비용이다.

### 예시
```json
"third_party_server_screen@common.base_screen": {
    "$screen_animations": [],
    "$background_animations": [],
    "$screen_content": "server_form.main_screen_content",
    "button_mappings": [
        {
            "from_button_id": "button.menu_cancel",
            "to_button_id": "button.menu_exit",
            "mapping_type": "global"
        }
    ]
}
```
- `$screen_animations`: 화면이 열리고 닫힐 때 붙는 애니메이션
- `$background_animations`: 뒷배경에 붙는 애니메이션
- 둘 다 빈 배열로 주면 애니메이션 없이 바로 뜬다.

### 좋은 점
- 폼 하나 닫고 바로 다음 폼을 여는 메뉴 이동이 훨씬 빨라 보인다.
- 열리는 동안 화면 전체를 움직이는 일이 없다.

### 주의할 점
- 등장 연출이 필요하면 화면 전체 말고 필요한 요소에만 `anim_type` 애니메이션을 직접 단다.
- NPC 대화창(`npc_interact_screen`)도 똑같이 끌 수 있다.
