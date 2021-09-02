<template>
	<p>
		지금까지 만들어온 코드를 비교해보고 싶으시다면, 깃헙 리포지토리에서
		확인하실 수 있습니다.
		<a :href="githubLink" target="_blank" rel="noopener noreferrer">
			깃헙 <OutboundLink />
		</a>
	</p>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import { usePageData } from "@vuepress/client";

const codeSamplesURL =
	"https://github.com/discordjs/guide/tree/main/code-samples/";

const props = defineProps({
	path: String,
});

const githubLink = ref(codeSamplesURL);
const page = usePageData();

onMounted(() => {
	githubLink.value +=
		props.path || page?.value.path.slice(1).replace(".html", "");
});
</script>
