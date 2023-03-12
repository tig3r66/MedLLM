<script lang="ts">
	import ChatMessage from '$lib/components/ChatMessage.svelte'
	import type { ChatCompletionRequestMessage } from 'openai'
	import { SSE } from 'sse.js'

	let query: string = ''
	let answer: string = ''
	let loading: boolean = false
	let chatMessages: ChatCompletionRequestMessage[] = []
	let scrollToDiv: HTMLDivElement

	function scrollToBottom() {
		setTimeout(function () {
			scrollToDiv.scrollIntoView({ behavior: 'smooth', block: 'end', inline: 'nearest' })
		}, 10)
	}

	const handleSubmit = async () => {
		if (query.trim() === "") {
			return;
		}

		loading = true
		chatMessages = [...chatMessages, { role: 'user', content: query }]

		const eventSource = new SSE('/api/chat', {
			headers: {
				'Content-Type': 'application/json'
			},
			payload: JSON.stringify({ messages: chatMessages })
		})

		query = ''
		const textarea = document.querySelector('textarea');
		textarea.style.height = `25px`;

		eventSource.addEventListener('error', handleError)

		eventSource.addEventListener('message', (e) => {
			scrollToBottom()
			try {
				loading = false
				if (e.data === '[DONE]') {
					chatMessages = [...chatMessages, { role: 'assistant', content: answer }]
					answer = ''
					return
				}

				const completionResponse = JSON.parse(e.data)
				const [{ delta }] = completionResponse.choices

				if (delta.content) {
					answer = (answer ?? '') + delta.content
				}
			} catch (err) {
				handleError(err)
			}
		})
		eventSource.stream()
		scrollToBottom()
	}

	function handleError<T>(err: T) {
		loading = false
		query = ''
		answer = ''
		console.error(err)
	}
</script>

<div class="flex flex-col h-screen pt-4 pb-4 w-full px-8 items-center gap-2">
	<div>
		<h1 class="text-2xl font-bold w-full text-center">MedLLM</h1>
		<!-- <p class="text-sm italic">Powered by gpt-3.5-turbo</p> -->
	</div>
	<div class="flex-1 w-full bg-gray-900 rounded-md p-4 overflow-y-auto flex flex-col gap-4">
		<div class="flex flex-col gap-2">
			<ChatMessage type="assistant" message="Welcome to MedLLM, ask me anything you want!" />
			{#each chatMessages as message}
				<ChatMessage type={message.role} message={message.content} />
			{/each}
			{#if answer}
				<ChatMessage type="assistant" message={answer} />
			{/if}
			{#if loading}
				<ChatMessage type="assistant" message="Thinking..." />
			{/if}
		</div>
		<div class="" bind:this={scrollToDiv} />
	</div>
	<form class="flex w-full rounded-md gap-4 bg-gray-900 p-4" on:submit|preventDefault={() => handleSubmit()}>
		<textarea
			class="input input-bordered w-full"
			bind:value={query}
			rows="2"
			style="min-height: 50px; height: auto; overflow: hidden; resize: none;"
			on:input={(e) => {
				const textarea = e.target;
				textarea.style.height = "auto";
				textarea.style.height = `${textarea.scrollHeight}px`;
			}}
			on:keydown={(e) => {
				if (e.key === "Enter" && !e.shiftKey) {
					e.preventDefault(); // prevent default behavior of inserting a newline
					handleSubmit();
				}
			}}
		></textarea>

		<button type="submit" class="btn btn-accent bg-sky-500/100"> Send </button>
	</form>
</div>
