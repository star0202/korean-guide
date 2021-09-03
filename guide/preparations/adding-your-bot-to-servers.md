# 서버에 봇 추가하기

[봇 애플리케이션을 설정](/preparations/setting-up-a-bot-application.md)했다면, 아직 봇이 어떤 서버에도 들어가 있지 않습니다. 어떻게 해서 봇을 추가하는 걸까요?

봇을 서버에서 보기 전에 봇의 client id를 이용해서 만든 초대 링크를 이용해 서버에 초대해야 합니다.

## 봇 초대 링크

일반적인 봇 초대 링크는 이렇게 생겼어요:

```:no-line-numbers
https://discord.com/api/oauth2/authorize?client_id=123456789012345678&permissions=0&scope=bot%20applications.commands
```

URL의 구조는 간단해요:

* `https://discord.com/api/oauth2/authorize` 는 Discord의 OAuth2 애플리케이션을 인증하는 표준 URL입니다.
* `client_id=...` 는 _어떤_ 애플리케이션을 인증할 지 나타냅니다. 유효한 봇 초대 링크를 만드려면 이 부분을 봇의 실제 client id로 바꿔야 해요.
* `permissions=...` 봇이 들어갈 서버에서 어떤 권한을 부여받을지를 나타냅니다.
* `scope=bot%20applications.commands` 이 애플리케이션을 슬래시 명령어를 사용할 수 있는 Discord 봇으로 인증하도록 합니다.

::: warning
"Bot requires a code grant" 에러를 보게 된다면 봇의 애플리케이션 설정에서 "Require OAuth2 Code Grant" 옵션을 꺼주세요. 일반적인 경우에는 이 옵션을 꺼야 해요.
:::

## 봇 초대 링크를 만들고 사용하기

봇 초대 링크를 만드려면, "Applications" 섹션 안의 [My Applications](https://discord.com/developers/applications/me)로 돌아가서 봇 애플리케이션을 클릭하고 왼쪽에서 "OAuth2"를 클릭하세요.

페이지 아래쪽에 Discord URL 생성기가 있어요.`bot`과 `applications.commands` 옵션을 선택하세요. `bot` 옵션을 선택하면 권한 리스트가 뜨는데 여기서 봇이 필요한 권한을 설정할 수 있어요.

"Copy" 버튼을 누른 다음 복사한 URL로 들어가보세요. 아래와 같은 화면을 보게 될 거에요(봇의 닉네임과 아바타는 초대하려는 봇에 맞게 바뀝니다):

![봇 인증 페이지](./images/bot-auth-page.png)

봇을 추가하려는 서버를 선택한 다음 "계속하기"를 누르세요. 그 다음 권한 목록이 뜨는데, 필요 없는 권한을 체크 해제한 다음 "인증"을 누르세요. 참고로 서버에 봇을 초대하려면 그 서버에서 "서버 관리하기" 권한이 필요해요. 그리고 캡챠를 풀면 봇이 초대됐다는 창이 뜰 거에요:

![봇 추가 완료](./images/bot-authorized.png)

축하드려요! Discord 서버에 봇을 추가했어요. 봇은 이런 식으로 서버 멤버 리스트에 뜰 거에요:

![서버 멤버 리스트에 있는 봇](./images/bot-in-memberlist.png)
