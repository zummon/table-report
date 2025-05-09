<script>
	let rawActs = ['','group','total']

	let data = $state([['head','desc','amount'],['type1','value',120],['type2','value',100],['type1','',80]])
	let acts = $state([])

	let report = $derived.by(() => {
		let body = [[]]
		let footers = []
		data.slice(1).forEach((row, rowindex) => {
			row.forEach((col, colindex) => {
				acts[colindex] == 'group'
				if (!body[rowindex]) {
					body[rowindex] = []
				}
				body[rowindex][colindex] = col
				if (acts[colindex] == 'total') {
					if (!footers[colindex]) {
						footers[colindex] = 0
					}
					footers[colindex] += +col
				}
			})
		})
		return [data[0], ...body, footers]
	});
</script>

Column <input type="number">
Subtotal <input type="checkbox">

<table>
	<thead>
		<tr>
			{#each data[0] as col, colindex}
				<td onchange={() => {}}>
					{col}
					<select value={acts[colindex]} onchange={(e) => {
						acts[colindex] = e.currentTarget.value
					}}>
						{#each rawActs as value}
							<option value={value}>{value}</option>
						{/each}
					</select>
				</td>
			{/each}
		</tr>
	</thead>
	<tbody>
	{#each report.slice(1) as row, rowindex}
		<tr>
			{#each row as col, colindex}
				<td onchange={() => {
	
				}}>{col}</td>
			{/each}
		</tr>
	{/each}
	</tbody>
</table>