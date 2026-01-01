<script>
	import { onMount } from "svelte";
	const multilingual = {th:{
		
	},en:{

	}}

	let user = $state({ lang: 'en' })
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
		return new Date(value).toLocaleDateString(user.lang, { day: 'numeric', month: 'long', year: 'numeric', weekday: 'long', ...option })
	}
	function upload(e, ) {
		const file = e.currentTarget.files[0];
		file.arrayBuffer().then((rawTrans) => {
			const fileTrans = read(rawTrans, { cellDates: true });
			const sheetName = fileTrans.SheetNames[0];
			const worksheet = fileTrans.Sheets[sheetName];
			let aoa = utils.sheet_to_json(worksheet, { header: 1 });
			spread = (aoa);
		});
	}

	onMount(() => {
		const params = new URLSearchParams(location.search)
		const lang = params.get('lang')
		if (multilingual[lang]) {
			user.lang = lang
		}
	})
</script>

<div class="print:hidden">
	
	<details>
		<summary class="list-none cursor-pointer">Upload</summary>
		<input type="file" accept="xlsx" onchange={(e)=>{
			upload(e)
		}}>
	</details>

	<details>
		<summary class="list-none cursor-pointer">Copy Paste</summary>
		<textarea onchange={(e)=>{e.target.value}} />
	</details>

	<button class="cursor-pointer" onclick={() => {
		print()
	}}>Print</button>
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
				}}>+</button>
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
				}}>+</button>
			</td>
		</tr>
	</tbody>
</table>

