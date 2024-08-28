# 각 모델에 대한 테스트 결과
## EEVE-Korean-10.8B:lastest
### 결론
- 요청한 출력 형식을 제대로 따르지 못하는 경우가 대부분이라 쓸 수 없음
### 예시
1. JSON 포맷을 제대로 이해하고 있지 않음: `modifica\tiions`이라는 잘못된 속성명을 사용함
```json
{
  "htmlOutput": "...",
  "modifica\tiions": []
}
```
2. 요청한 속성 값에 대한 지시를 제대로 따르지 않음: is_multidata_mode에 `false`가 아닌, `faals`라는 속성명을 사용
```html
<section> \n <div class='row' name='repair_request' alias='고장 신고' is_multidata_mode='faals'> \n <div class='col-sm-12'> \n
```
## deepseek-coder:33b
### 결론
- 요청한 내용에 대해서 전혀 엉뚱한 결과를 생성해서 쓸 수 없음
### 예시
1. 요청한 출력 형식이 아닌, 완전히 다른 형식의 엉뚱한 답을 생성함
- 요청한 응답 형식
````
응답 시에는 절차나 과정 같은 것은 따로 설명하지 말고, 바로 JSON 응답만 출력하도록 하면 돼.
JSON 응답은 markdown 으로, three backticks 로 감싸줘야 내가 이 결과를 사용해야 한다는 걸 알 수 있으니까 명심해.
```
{
"htmlOutput": "생성된 폼 HTML 코드"
}
```
````
- AI가 응답한 형식
```json
{
    "forms": [
        {
            "form_alias": "회원가입",
            "fields": [
                {
                    "field_name": "username",
                    "alias": "사용자 이름"
                },
                {
                    "field_name": "email",
                    "alias": "이메일"
                },
                {
                    "field_name": "password",
                    "alias": "비밀번호"
                },
                {
                    "field_name": "confirm_password",
                    "alias": "비밀번호 확인"
                }
            ]
        }
    ]
}

```