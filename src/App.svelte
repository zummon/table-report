<script>
	import { onMount } from "svelte";

	let user = $state({ t: [], c: ["ลำดับ\nที่","ประเภท\nการจ่าย","หมวด\nรายการ",'รายการ',"จำนวนเงิน\nบาท/ส.ต.",'ภาษี','ค่าปรับ','รหัสบัญชี',"เลขที่ใบสั่งซื่อ\nGFMIS","เลขที่เอกสาร\nอ้างอิง","เลขที่\nกันเงิน","ชื่อ/บริษัทผู้ขาย\nผู้รับเงิน"] })
	let pick = $state('')
	let data = $state([])
	let lines = $state([20])

	let list = $derived.by(() => {
		let set = new Set()
		data.forEach((row, rowindex) => {
			set.add(row[0])
		})
		return [...set]
	})
	let reports = $derived.by(() => {
		let result = [[]]
		let lineindex = 0
		data.forEach((row, rowindex) => {
			if (!pick || pick == row[0]) {
				if (!lines[lineindex] || lines[lineindex] > result.at(-1).length) {
					result.at(-1).push(row.slice(1))
				} else {
					result.push([row.slice(1)])
					lineindex++
				}
			}
		})
		return result
	});

	function arrize(str) {
		let result = []
		try {
			str.split('\n').forEach((row, rowindex) => {
				result[rowindex] = []
				row.split('	').forEach((col, colindex) => {
					result[rowindex][colindex] = col
				})
			})
		} catch {

		}
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

<datalist id="list">
	{#each list as value}
		<option value={value}>{value}</option>
	{/each}
</datalist>

<div class="print:hidden">
	<textarea class="border" onchange={(e) => {
		let value = e.currentTarget.value
		data = arrize(value)
	}}></textarea>

	<input class="border" type="text" placeholder="yyyy-mm-dd" list="list" value={pick} onchange={(e) => {
		let value = e.currentTarget.value
		pick = value
	}}>
	<button class="cursor-pointer" onclick={() => { 
		pick = ''
	}}>{@render xicon()}</button>

	<button class="cursor-pointer" onclick={() => { 
		lines.push(20)
	}}>{@render plusicon()}</button>
	{#each lines as line, index}
		<input class="border field-sizing-content" type="number" min="1" value={line} onchange={(e) => {
			let value = e.currentTarget.value
			lines[index] = Number(value)
		}}>
		<button class="cursor-pointer" onclick={() => { 
			lines.splice(index, 1)
		}}>{@render xicon()}</button>
	{/each}

	<button class="cursor-pointer" onclick={() => {
		print()
	}}>{@render printicon()}</button>
</div>

{#each reports as report, index}
	<table class="text-xs mx-auto w-[1240px] print:w-full break-after-page">
		<tbody class="">
			{#each user.t as content}
				<tr class="text-center">
					<td class="relative" colspan="12">
						{content}
						{#if reports.length > 1}
							<span class="absolute right-0 top-0">หน้าที่ {index}/{reports.length}</span>
						{/if}
					</td>
				</tr>
			{/each}
			<tr class="text-center">
				<td class="" colspan="12">
					ประจำ{datize(pick)}
				</td>
			</tr>
			<tr class="text-center">
				{#each user.c as content}
					<td class="border whitespace-pre">{content}</td>
				{/each}
			</tr>
			{#each report as row, rowindex}
				<tr class="border-b border-dotted font-thin">
					{#each row as col, colindex}
						<td class="border-x p-1">{col}</td>
					{/each}
				</tr>
			{/each}
		</tbody>
	</table>
{/each}

<!-- https://heroicons.com/micro x-mark -->
{#snippet xicon()}
	<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" fill="currentColor" class="size-8">
		<path d="M5.28 4.22a.75.75 0 0 0-1.06 1.06L6.94 8l-2.72 2.72a.75.75 0 1 0 1.06 1.06L8 9.06l2.72 2.72a.75.75 0 1 0 1.06-1.06L9.06 8l2.72-2.72a.75.75 0 0 0-1.06-1.06L8 6.94 5.28 4.22Z" />
	</svg>
{/snippet }
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