<script>
	import { onMount } from "svelte";

	let user = $state({ t: [], })
	let data = $state([['head','desc','amount'],['type1','value',120],['type2','value',100],['type1','',80]])

	let reports = $derived.by(() => {
		let body = [[]]
		let footers = []
		data.slice(1).forEach((row, rowindex) => {
			row.forEach((col, colindex) => {

			})
		})
		return []
	});

	function arrize(rows) {
		let result = [[]]
		rows.split('\n').forEach((row, rowindex) => {
			result[rowindex] = []
			row.split('	').forEach((col, colindex) => {
				result[rowindex][colindex] = col
			})
		})
		return result
	}

	onMount(() => {
		let params = new URLSearchParams()
		for (let key in user) {
			user[key] = params.getAll(key)
		}
	})
</script>

<textarea class="" onchange={(e)=>{
	let value = e.currentTarget.value
	data = arrize(value)
}}></textarea>

{#each reports as report}
	<table class="">
		<tbody class="">
			{#each report as row, rowindex}
				<tr>
					{#each row as col, colindex}
						<td>{col}</td>
					{/each}
				</tr>
			{/each}
		</tbody>
	</table>
{/each}