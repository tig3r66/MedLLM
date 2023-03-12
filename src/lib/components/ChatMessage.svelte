<script lang="ts">
	import type { ChatCompletionRequestMessageRoleEnum } from 'openai'
	export let type: ChatCompletionRequestMessageRoleEnum
	export let message: string
  
	function getIndentationLevel(line: string): number {
	  const match = line.match(/^([ \t]*)/)
	  if (match) {
		const indentation = match[1]
		const tabSize = 2 // number of spaces per tab
		return indentation.replace(/\t/g, ' '.repeat(tabSize)).length
	  } else {
		return 0
	  }
	}
</script>


<div class="chat {type === 'user' ? 'chat-end' : 'chat-start'} justify-end">
	<div class="chat-image avatar">
		<div class="w-10 rounded-full">
		<img src="https://ui-avatars.com/api/?name={type === 'user' ? 'Me' : 'M'}" alt="{type} avatar" />
		</div>
	</div>
	<div class="chat-header"></div>
	<div class="chat-bubble {type === 'user' ? 'chat-bubble-primary bg-sky-500/100' : 'chat-bubble-secondary bg-gray-500'}">
		{#each message.split('```') as block, i}
			{#if i % 2 === 0}
				{#each block.split('\n') as line, j}
					{#if line.trim()}
						<div style="white-space: pre-wrap; word-wrap: break-word">
						{Array(getIndentationLevel(line)).fill('\u00a0').join('')}{line.trim()}
						{#if j !== block.split('\n').length - 1}
							<br>
						{/if}
						</div>
					{/if}
				{/each}
			{:else}
				<pre>{block}</pre><br />
			{/if}
		{/each}
	</div>
</div>
