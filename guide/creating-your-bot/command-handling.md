# 커맨드 핸들링

봇 프로젝트가 작은게 아니라면, 커맨드 핸들링을 위해 가슴이 웅장해지는 `if`/`else if` 군단을 한 파일에 우겨넣는 것은 전혀 좋은 방법이 아닙니다. 만약 봇에 여러 기능들을 구현하고 개발 과정을 덜 괴롭게 만들고 싶다면, 커맨드 핸들러를 구현해야 합니다. 시작해봅시다!

아래는 우리가 사용할 기본적인 파일들과 코드입니다:

:::: code-group
::: code-group-item index.js

```js
const { Client, Intents } = require("discord.js");
const { token } = require("./config.json");

const client = new Client({ intents: [Intents.FLAGS.GUILDS] });

client.once("ready", () => {
	console.log("Ready!");
});

client.on("interactionCreate", async (interaction) => {
	if (!interaction.isCommand()) return;

	const { commandName } = interaction;

	if (commandName === "ping") {
		await interaction.reply("Pong!");
	} else if (commandName === "beep") {
		await interaction.reply("Boop!");
	}
});

client.login(token);
```

:::
::: code-group-item deploy-commands.js

```sh:no-line-numbers
npm install @discordjs/rest discord-api-types
```

```js
const { REST } = require("@discordjs/rest");
const { Routes } = require("discord-api-types/v9");
const { clientId, guildId, token } = require("./config.json");

const commands = [];

const rest = new REST({ version: "9" }).setToken(token);

(async () => {
	try {
		await rest.put(Routes.applicationGuildCommands(clientId, guildId), {
			body: commands,
		});

		console.log("애플리케이션 커맨드를 성공적으로 추가했습니다.");
	} catch (error) {
		console.error(error);
	}
})();
```

:::
::: code-group-item config.json

```json
{
	"clientId": "123456789012345678",
	"guildId": "876543210987654321",
	"token": "여기-토큰-붙여넣기"
}
```

:::
::::

## 커맨드 파일

프로젝트 폴더는 아래와 같은 형식으로 하세요:

```:no-line-numbers
discord-bot/
├── node_modules
├── config.json
├── deploy-commands.js
├── index.js
├── package-lock.json
└── package.json
```

모든 커맨드들을 저장할 `commands`라는 이름의 폴더를 만드세요.

슬래쉬 커맨드 데이터를 만들기 위해 [`@discordjs/builders`](https://github.com/discordjs/builders) 패키지를 사용할 것입니다. 터미널을 열고 설치해주세요.

```sh:no-line-numbers
npm install @discordjs/builders
```

그 다음, 핑 커맨드를 위해 `commands/ping.js` 파일을 만드세요.

```js
const { SlashCommandBuilder } = require("@discordjs/builders");

module.exports = {
	data: new SlashCommandBuilder().setName("ping").setDescription("핑퐁!"),
	async execute(interaction) {
		await interaction.reply("pong!");
	},
};
```

이제 다른 커맨드들도 각 코드들을 `execute()` 함수에 넣어 똑같이 만들어 주면 됩니다.

::: tip
[`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports)는 Node.js에서 데이터를 내보내어 다른 파일에서 [`require()`](https://nodejs.org/api/modules.html#modules_require_id) 할 수 있게 합니다.

만약 커맨드 파일 안에서 client 인스턴스에 접근해야 한다면, `interaction.client`로 접근할 수 있습니다. 만약 외부 파일, 패키지 등에 접근해야 한다면 파일 상단에 `require()`을 사용하세요.
:::

## 커맨드 파일 읽어오기

`index.js` 파일 내에서, 아래 코드를 추가하세요:

```js {1-2,7}
const fs = require("fs");
const { Client, Collection, Intents } = require("discord.js");
const { token } = require("./config.json");

const client = new Client({ intents: [Intents.FLAGS.GUILDS] });

client.commands = new Collection();
```

::: tip
[`fs`](https://nodejs.org/api/fs.html)는 Node의 네이티브 파일 시스템 모듈입니다. <DocsLink section="collection" path="class/Collection" />은 자바스크립트 네이티브의 [`Map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 클래스를 상속한 클래스로, 더 많은 편리한 기능이 포함되어 있습니다.
:::

그 다음 단계는 커맨드 파일들을 동적으로 가져오는 것입니다. [`fs.readdirSync()`](https://nodejs.org/api/fs.html#fs_fs_readdirsync_path_options) 메소드는 디렉토리 안의 모든 파일 이름의 배열을 반환할 것입니다. (예: `['ping.js', 'beep.js']`) 오직 커맨드 파일만 얻기 위해, 배열에서 자바스크립트가 아닌 파일들은 `Array.filter()`을 사용해 걸러냅니다. 이 배열로 반복문을 돌려 `client.commands` 컬렉션에 커맨드를 동적으로 설정합니다.

```js {2,4-9}
client.commands = new Collection();
const commandFiles = fs
	.readdirSync("./commands")
	.filter((file) => file.endsWith(".js"));

for (const file of commandFiles) {
	const command = require(`./commands/${file}`);
	// 컬랙션에 새 요소를 설정합니다.
	// 커맨드 이름을 키로 하고 불러온 모듈을 값으로 합니다.
	client.commands.set(command.data.name, command);
}
```

`deploy-commands.js`에서 각 커맨드들의 JSON 데이터 배열인 `commands`에 `.push()`를 합니다.

```js {1,7,9-12}
const fs = require("fs");
const { REST } = require("@discordjs/rest");
const { Routes } = require("discord-api-types/v9");
const { clientId, guildId, token } = require("./config.json");

const commands = [];
const commandFiles = fs
	.readdirSync("./commands")
	.filter((file) => file.endsWith(".js"));

for (const file of commandFiles) {
	const command = require(`./commands/${file}`);
	commands.push(command.data.toJSON());
}
```

## 동적으로 커맨드 실행하기

`client.commands` 컬랙션을 사용하여 커맨드를 가져와서 실행할 수 있습니다! `interactionCreate` 이벤트 안에 `if`/`else if` 군단을 지우고 아래로 대체하세요.

```js {4-13}
client.on("interactionCreate", async (interaction) => {
	if (!interaction.isCommand()) return;

	const command = client.commands.get(interaction.commandName);

	if (!command) return;

	try {
		await command.execute(interaction);
	} catch (error) {
		console.error(error);
		await interaction.reply({
			content: "커맨드 실행 중에 오류가 발생했습니다.",
			ephemeral: true,
		});
	}
});
```

첫번째로, 컬렉션에서 커맨드 이름으로 커맨드를 가져와 `command` 변수에 할당합니다. 만약 커맨드가 존재하지 않는다면, `undefined`를 반환할 것이므로 `return`으로 바로 함수를 끝냅니다. 만약 존재한다면, 커맨드의 `.execute()` 메소드를 호출하고 매개변수로 `interaction` 변수를 넘깁니다. 무언가 오류가 발생했을 경우에는, 에러를 출력하고 사용자에게 오류가 발생했음을 알립니다.

이게 끝입니다! 새로운 커맨드를 추가하고 싶다면 `commands` 디렉토리에 슬래쉬 커맨드 이름으로 새 파일을 만들고 다른 커맨드처럼 만드시면 됩니다.`node deploy-commands.js`을 실행해서 커맨드들을 등록해야 함을 잊지 마세요!

## 결과 코드

<ResultingCode />
