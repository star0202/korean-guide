# 초기 설정

[add your bot to a server](/preparations/adding-your-bot-to-servers.md)를 끝내고 오셨다면, 다음 단계는 코딩을 시작하고 봇을 온라인으로 만드는 것입니다! 클라이언트 토큰을 위해 설정 파일을 만들고 봇 애플리케이션을 위해 메인 파일을 만들며 시작해봅시다.

## 설정 파일 만들기

["What is a token, anyway?"](/preparations/setting-up-a-bot-application.md#what-is-a-token-anyway") 부분에서 설명되었다시피 토큰은 봇의 비밀번호와 같습니다. 그러므로 최대한 유출되지 않도록 보호해야 합니다. `config.json` 파일이나 환경 변수를 설정하여 보호할 수 있습니다.

[Discord Developer Portal](https://discord.com/developers/applications)에서 당신의 애플리케이션을 여시고 "Bot" 페이지로 가서 토큰을 복사하세요.

### `config.json` 사용하기

`config.json` 파일에 데이터를 저장하는 것은 중요한 데이터를 안전히 저장하는 일반적인 방법 중 하나입니다. `config.json` 파일을 프로젝트 폴더에 생성하고 토큰을 붙여넣기 하세요. `require()`을 사용하여 다른 파일에서 토큰을 얻을 수 있습니다.

:::: code-group
::: code-group-item config.json

```json
{
	"token": "여기-토큰-붙여넣기"
}
```

:::
::: code-group-item 사용방법

```js
const { token } = require("./config.json");

console.log(token);
```

:::
::::

::: danger
만약 Git을 사용하신다면, 이 파일을 절대 커밋해서는 안됩니다. [`.gitignore`을 사용하여 무시](/creating-your-bot/#git과-gitignore)하도록 해야 합니다.
:::

### 환경변수 사용하기

환경변수는 환경(예: 터미널 세션, 도커 컨테이너 또는 환경 변수 파일 등)을 위한 특별한 변수들입니다.

환경변수를 넘기는 한가지 방법은 커맨드 라인을 사용하는 것입니다. 앱을 사직할때 `node index.js` 대신 `TOKEN=여기-토큰-붙여넣기`을 사용하세요. 다른 변수를 넘기는데도 이런 패턴을 반복할 것입니다.

그리고 코드에서 `process.env` 전역변수를 통해 모든 파일에서 접근할 수 있습니다. 이렇게 넘겨진 값들은 항상 문자열이여서 계산이 필요하면 숫자로 바꿔줄 필요가 있다는 사실을 기억해야 합니다.

:::: code-group
::: code-group-item 커맨드 라인

```sh:no-line-numbers
A=123 B=456 TOKEN=여기-토큰-붙여넣기 node index.js
```

:::
::: code-group-item 사용방법

```js
console.log(process.env.A);
console.log(process.env.B);
console.log(process.env.TOKEN);
```

:::
::::

#### dotenv 사용하기

또 다른 일반적인 방법은 `.env` 파일에 이런 값들을 저장하는 것입니다. 이것은 귀찮게 커맨드 라인에 토큰을 복사하지 않아도 되게 해줍니다. `.env.` 파일의 각 줄들은 `KEY=value` 쌍으로 작성해야 합니다.

[`dotenv` 패키지](https://www.npmjs.com/package/dotenv)를 사용하세요. 설치되면, 패키지를 require하고 `.env` 파일을 로드하도록 하여 `process.env`에 변수들을 저장합니다:

```sh:no-line-numbers
npm install dotenv
```

:::: code-group
::: code-group-item .env

```
A=123
B=456
TOKEN=여기-토큰-붙여넣기
```

:::
::: code-group-item 사용방법

```js
const dotenv = require("dotenv");

dotenv.config();

console.log(process.env.A);
console.log(process.env.B);
console.log(process.env.TOKEN);
```

:::
::::

::: danger
만약 Git을 사용하신다면, 이 파일을 절대 커밋해서는 안됩니다. [`.gitignore`을 사용하여 무시](/creating-your-bot/#git과-gitignore)하도록 해야 합니다.
:::

::: details 온라인 에디터 (Glitch, Heroku, Repl.it, etc.)
온라인 에디터를 호스팅으로 사용하는 것보다 적절한 가상 사설 서버 호스팅에 투자하는 것을 더 권장하지만, 이 서비스들도 중요한 정보를 안전히 저장하는 방법을 제공합니다! 민감한 정보를 어떻게 안전하게 보호하는지에 대한 더 많은 정보는 아래 각 서비스의 문서와 글들을 보세요.

-   Glitch.com: [Storing secrets in .env](https://glitch.happyfox.com/kb/article/18)
-   Heroku.com: [Configuration variables](https://devcenter.heroku.com/articles/config-vars)
-   Repl.it: [Secrets and environment variables](https://docs.replit.com/repls/secrets-environment-variables)

:::

### Git과 `.gitignore`

깃은 코드 변경을 추적하고 [GitHub](https://github.com/), [GitLab](https://about.gitlab.com/), 또는 [Bitbucket](https://bitbucket.org/product) 같은 서비스들에 업로드할 수 있는 매우 멋진 툴입니다. 깃이 다른 개발자들과 공유할 때 사용하기 매우 좋은 툴이지만, 이것은 민감한 정보들을 담은 설정 파일을 업로드하는 위험을 감수해야 합니다.

`.gitignore` 파일로 버전 관리 시스템에서 제외해야 할 파일들을 명시할 수 있습니다. 프로젝트 폴더에 `.gitignore` 파일을 만들고 제외시키고 싶은 파일 또는 폴더의 이름을 추가하세요.

```
node_modules
.env
config.json
```

::: tip
중요한 정보를 안전하게 저장하는 것 외에도, `node_modules`도 `.gitignore`에 추가되어야 합니다. `npm install`을 실행하여 `package.json`과 `package-lock.json` 파일을 통해 `node_modules` 폴더를 복원할 수 있으므로 깃에 포함시킬 필요가 없습니다.

`.gitignore` 파일에서 더 복잡한 패턴을 명시할 수 있습니다. 더 많은 정보는 [`.gitignore`에 관한 Git 문서](https://git-scm.com/docs/gitignore)를 확인하세요.
:::

## 메인 파일 만들기

코드 에디터를 여시고 새 파일을 만드세요. 파일의 이름은 `index.js`로 정하는 것이 좋지만 원하시는 이름 아무거나 사용하셔도 괜찮습니다.

아래는 프로젝트를 시작할 가장 기초적인 코드입니다.

```js
// 필요한 discord.js 클래스를 require합니다.
const { Client, Intents } = require("discord.js");
const { token } = require("./config.json");

// 새 client 인스턴스를 생성합니다.
const client = new Client({ intents: [Intents.FLAGS.GUILDS] });

// 클라이언트가 준비되면, 코드를 실행합니다. (딱 한번만)
client.once("ready", () => {
	console.log("Ready!");
});

// 디스코드를 클라이언트의 토큰으로 로그인합니다.
client.login(token);
```

위는 봇의 클라이언트 인스턴스를 생성하고 디스코드에 로그인하는 코드입니다. `Intents.FLAGS.GUILDS` 인텐트는 클라이언트가 잘 작동하기 위해 필수적인 옵션입니다.

터미널을 여시고 `node index.js`를 실행하여 봇 프로세스를 시작하세요. 만약 몇 초 후 "Ready!"가 출력됐다면, 성공하셨습니다!

::: tip
`package.json` 파일을 열어 메인 파일을 지정하기 위해 `"main": "index.js"` 필드를 수정할 수 있습니다. 그리고 터미널에서 `node .`을 실행하여 봇 프로세스를 시작할 수 있습니다.

`Ctrl-C`로 프로세스를 닫은 후 위쪽 화살표 키를 눌러 가장 최근에 실행한 커맨드를 가져올 수 있습니다. 위쪽 화살표 키 누르고 엔터를 누르는 것이 프로세스를 다시 시작하는 가장 빠른 방법입니다.
:::

## 결과 코드

<ResultingCode path="creating-your-bot/initial-files" />
