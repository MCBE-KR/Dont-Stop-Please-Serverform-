# low_frequency_rendering
[◀](../README.md)

화면 렌더링에 메모리를 덜 쓰게 해주는 옵션이다.
크게 효과가 있는지는 모르겠다.

서버폼에 적용하려면 third_party_server_screen 패널에 넣으면 된다.

### 예시
```json
"third_party_server_screen@common.base_screen": {
    "low_frequency_rendering": true,
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

[bedrock wiki](https://wiki.bedrock.dev/json-ui/json-ui-documentation#screen)