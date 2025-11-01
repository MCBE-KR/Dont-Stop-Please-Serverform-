# stack_panel를 이용하여 패널 삭제하기
[◀](../README.md)

#### 예시
```json
{
    "type": "stack_panel",
    "collection_name": "test",

    "factory": {
        "name": "test_factory",
        "control_name": "<메인 패널>"
    },
    "bindings": [
        {
            "binding_type": "view",
            "source_property_name": "(<폼 표시 조건식> * 1) * 1)",
            "target_property_name": "#collection_length"
        }
    ]
}
```

이 방법은 stack_panel의 특징을 이용한 것으로
stack_panel은 #collection_length에 수를 대입하면 그 수만큼 패널 수를 변경시킬 수 있다.
그리고 내부에 패널이 있는 상태에서 #collection_length가 0이 되면 **내부 패널을 삭제시킨다.**

이 원리를 이용하여 <폼 표사 조건식>을 만족했다면 #collection_length를 1로
만족하지 않았다면 0으로 바꾸면서 렌더링 되지 않은 상태일 때 패널을 삭제하는 것

하지만 문제는 stack_panel과 factory 자체의 비용이 많이 높다는 것이다.
`메인 패널의 비용 > stack_panel + factory의 비용` 일때만 사용하는 것을 추천함