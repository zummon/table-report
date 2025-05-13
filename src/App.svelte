<script>
	import { onMount } from "svelte";

	let user = $state({ t: [], c: [] })
	let spread = $state([['','','',''],['','','',''],['','','',''],['','','','']])

	function arrize(str) {
		let result = []
		str.split('\n').forEach((row, rowindex) => {
			result[rowindex] = []
			row.split('\t').forEach((col, colindex) => {
				result[rowindex][colindex] = col
			})
		})
		return result
	}
	function datize(value, option) {
		return new Date(value).toLocaleDateString('th', { day: 'numeric', month: 'long', year: 'numeric', weekday: 'long', ...option })
	}

	onMount(() => {
		let params = new URLSearchParams()
		for (let key in user) {
			let value = params.get(key)
			if (value) {
				user[key] = value.split(',')
			}
		}
	})
</script>

<dialog id="disk">
	<button class="cursor-pointer" onclick={() => {
		document.getElementById('disk').close()
	}}>{@render xicon()}</button>
	<textarea class="border" placeholder="" onchange={(e) => {
		let value = e.currentTarget.value
		spread = arrize(value)
	}}></textarea>
</dialog>

<div class="print:hidden">
	<button class="cursor-pointer" onclick={() => {
		document.getElementById('disk').showModal()
	}}>{@render diskicon()}</button>
	<button class="cursor-pointer" onclick={() => {
		print()
	}}>{@render printicon()}</button>
</div>

<table class="">
	<tbody class="">
		<tr class="">
			<td class=""></td>
			{#each spread[0] as cell, colindex}
				<td class="border px-1">
					<span contenteditable="true" bind:textContent={spread[0][colindex]}></span>
				</td>
			{/each}
			<td class="">
				<button class="cursor-pointer" onclick={() => {
					spread.forEach((row, rowindex) => {
						spread[rowindex].push('')
					})
				}}>{@render plusicon()}</button>
			</td>
		</tr>
		{#each spread.slice(1) as cells, rowindex}
			<tr class="">
				<td class=""></td>
				{#each cells as cell, colindex}
					<td class="border px-1" contenteditable="true" bind:textContent={spread[rowindex +1][colindex]}></td>
				{/each}
			</tr>
		{/each}
		<tr class="">
			<td class="">
				<button class="cursor-pointer" onclick={() => {
					let row = spread[0].map(() => '')
					spread.push(row)
				}}>{@render plusicon()}</button>
			</td>
		</tr>
	</tbody>
</table>


<!-- https://heroicons.com/micro x-mark -->
{#snippet xicon()}
	<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" fill="currentColor" class="size-8">
		<path d="M5.28 4.22a.75.75 0 0 0-1.06 1.06L6.94 8l-2.72 2.72a.75.75 0 1 0 1.06 1.06L8 9.06l2.72 2.72a.75.75 0 1 0 1.06-1.06L9.06 8l2.72-2.72a.75.75 0 0 0-1.06-1.06L8 6.94 5.28 4.22Z" />
	</svg>
{/snippet}
<!-- plus -->
{#snippet plusicon()}
	<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" fill="currentColor" class="size-8">
		<path d="M8.75 3.75a.75.75 0 0 0-1.5 0v3.5h-3.5a.75.75 0 0 0 0 1.5h3.5v3.5a.75.75 0 0 0 1.5 0v-3.5h3.5a.75.75 0 0 0 0-1.5h-3.5v-3.5Z" />
	</svg>
{/snippet}
<!-- https://heroicons.com/solid printer -->
{#snippet printicon()}
	<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-8">
		<path fill-rule="evenodd" d="M7.875 1.5C6.839 1.5 6 2.34 6 3.375v2.99c-.426.053-.851.11-1.274.174-1.454.218-2.476 1.483-2.476 2.917v6.294a3 3 0 0 0 3 3h.27l-.155 1.705A1.875 1.875 0 0 0 7.232 22.5h9.536a1.875 1.875 0 0 0 1.867-2.045l-.155-1.705h.27a3 3 0 0 0 3-3V9.456c0-1.434-1.022-2.7-2.476-2.917A48.716 48.716 0 0 0 18 6.366V3.375c0-1.036-.84-1.875-1.875-1.875h-8.25ZM16.5 6.205v-2.83A.375.375 0 0 0 16.125 3h-8.25a.375.375 0 0 0-.375.375v2.83a49.353 49.353 0 0 1 9 0Zm-.217 8.265c.178.018.317.16.333.337l.526 5.784a.375.375 0 0 1-.374.409H7.232a.375.375 0 0 1-.374-.409l.526-5.784a.373.373 0 0 1 .333-.337 41.741 41.741 0 0 1 8.566 0Zm.967-3.97a.75.75 0 0 1 .75-.75h.008a.75.75 0 0 1 .75.75v.008a.75.75 0 0 1-.75.75H18a.75.75 0 0 1-.75-.75V10.5ZM15 9.75a.75.75 0 0 0-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 0 0 .75-.75V10.5a.75.75 0 0 0-.75-.75H15Z" clip-rule="evenodd" />
	</svg>
{/snippet}
<!-- circle-stack -->
{#snippet diskicon()}
	<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-8">
		<path d="M21 6.375c0 2.692-4.03 4.875-9 4.875S3 9.067 3 6.375 7.03 1.5 12 1.5s9 2.183 9 4.875Z" />
		<path d="M12 12.75c2.685 0 5.19-.586 7.078-1.609a8.283 8.283 0 0 0 1.897-1.384c.016.121.025.244.025.368C21 12.817 16.97 15 12 15s-9-2.183-9-4.875c0-.124.009-.247.025-.368a8.285 8.285 0 0 0 1.897 1.384C6.809 12.164 9.315 12.75 12 12.75Z" />
		<path d="M12 16.5c2.685 0 5.19-.586 7.078-1.609a8.282 8.282 0 0 0 1.897-1.384c.016.121.025.244.025.368 0 2.692-4.03 4.875-9 4.875s-9-2.183-9-4.875c0-.124.009-.247.025-.368a8.284 8.284 0 0 0 1.897 1.384C6.809 15.914 9.315 16.5 12 16.5Z" />
		<path d="M12 20.25c2.685 0 5.19-.586 7.078-1.609a8.282 8.282 0 0 0 1.897-1.384c.016.121.025.244.025.368 0 2.692-4.03 4.875-9 4.875s-9-2.183-9-4.875c0-.124.009-.247.025-.368a8.284 8.284 0 0 0 1.897 1.384C6.809 19.664 9.315 20.25 12 20.25Z" />
	</svg>
{/snippet}
