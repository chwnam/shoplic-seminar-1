# 쇼플릭 세미나 #1: 모듈화된 워드프레스 플러그인 개발방법  

2024년 2월 29일에 열린 쇼플릭 오픈 세미나 #1 2번 세션 '모듈화된 워드프레스 플러그인 개발방법'의 예제 코드입니다.

- 발표자료: [구글 슬라이드](https://docs.google.com/presentation/d/19NXunMZyEugRaW8TcjqJ3RIwG4VAKvCjLt9b8vpszUA/edit?usp=sharing)

## 코드 설명
일반적인 워드프레스 플러그인의 설치법과 동일합니다. 적절히 개발용으로 설정된 워드프레스의 `wp-content/plugins` 디렉토리에 위치시키세요.
단, 절대 이 플러그인을 운용중인 워드프레스 설치본에서 활성화하지 마세요.

새 페이지를 생성하고 거기에 적절히 내용을 작성합니다. 이 때 쇼트코드 `[seminar_notice_1]`, `[seminar_notice_2]`를 포함해 주세요.
쇼트코드는 각각 파라미터 `p1`, `p2`을 선택적으로 받을 수 있습니다. 그러므로 각 쇼트코드는 아래처럼 작성할 수도 있습니다.

```
[seminar_notice_1 p1="foo" p2="bar"]

[seminar_notice_2 p1="play" p2="hard"]
```

## 눈여겨 보아야 할 지점
우선 프로그램이 제대로 동작하는지 살펴보려면 아래 파일들을 눈여겨 보시는 것을 추천드립니다.

### conf/*
- `module-args.php`: 파일에서 지정된 제목 접두사가 어떻게 쇼트코드까지 전달되는지를 파악합니다.
- `module-setup.php`: 컨테이너에게 어떤 모듈을 언제 실행할지를 지정하는 것을 파악합니다.

`Shortcodes` 클래스는 컨테이너에 적재될 때 `shortcodes` (첫글자는 소문자) 라는 프로퍼티 이름을 부여받습니다.
그러므로 `module-args`에서는 `shortcodes`로 접근합니다. 생성자엑 전달될 파라미터를 배열 형대로 지정합니다 (콜백 함수도 가능합니다).

`module-setup.php`에서는 각 코어의 원하는 do_action() 지점의 훅을 나열하고, 해당 훅의 콜백으로 사용될 모듈을 지정합니다.
우선순위를 조정할 수 있습니다.

## include/Modules/*
여기의 모듈 클래스는 `Module` 인터페이스를 구현합니다 (그렇지 않으면 컨테이너가 적재하지 않습니다).
`Ajax`는 AJAX 요청 처리를 담당하며, `Shortcodes`는 쇼트코드 처리를 담당합니다.
각 코드는 찬찬히 읽어보시기 바랍니다.

## includes/Supports/*
모듈은 모듈의 동작을 추상적으로 구현합니다. 쉽게 말해, 이 액션/필터의 콜백에서 이렇게 할 거다~ 저렇게 할 거다~ 정도로 두루뭉술하게 코딩됩니다.
보다 구체적인 것들은 여기 Support 클래스들에서 실현됩니다.
모듈과 서포드간의 연결, 그리고 컨테이너에서 어떻게 생성자를 통해 의존성이 주입되는지를 염두하면서 코드를 읽어가시기 바랍니다.
