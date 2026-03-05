<script>
	let { spread } = $props();

	let body = $derived.by(() => {
		let result = [];
		spread.slice(4, -1).forEach((cells) => {
			if (cells[7]) {
				result.at(-1).push(cells);
			} else {
				result.push([cells]);
			}
		});

		return result;
	});
	let header = $derived(spread.slice(0, 4));
	let footer = $derived(spread.slice(-1));
</script>

{#if body.length > 0 && header.length > 0 && footer.length > 0}
	<table class="w-full text-sm">
		<thead class="">
			<tr class="">
				<td class="text-center" colspan="8">
					{header[0][0]}
				</td>
			</tr>
			<tr class="">
				<td class="text-center" colspan="8">
					{header[1][0]}
				</td>
			</tr>
			<tr class="">
				<td class="text-center" colspan="8">
					{header[2][0]}
				</td>
			</tr>
			<tr class="text-center">
				<td class="border-l border-r border-t border-b px-1">
					{header[3][0]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[3][1]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[3][2]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[3][3]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[3][4]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[3][5]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[3][6]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[3][7]}
				</td>
			</tr>
		</thead>
		{#each body as data}
			{@const content = data.slice(0, -1)}
			{@const subtotal = data.slice(-1)}
			<tbody class="break-inside-avoid-page">
				{#each content as cells, rowindex}
					{@const isFirstRow = rowindex == 0}
					{@const availableBefore = content[rowindex - 1] || []}
					<tr class={isFirstRow ? "" : "font-extralight"}>
						<td
							class="border-l border-r text-center text-nowrap px-1"
							style={availableBefore[0] != cells[0] ? "border-bottom: 1px dotted; border-top: 1px dotted;" : ''}
						>
							{#if availableBefore[0] != cells[0]}
								{cells[0]}
							{/if}
						</td>
						<td
							class="border-l border-r text-center text-nowrap px-1"
							style={availableBefore[1] != cells[1] ? "border-bottom: 1px dotted; border-top: 1px dotted;" : ''}
						>
							{#if availableBefore[1] != cells[1]}
								{cells[1]}
							{/if}
						</td>
						<td
							class="border-l border-r text-center text-nowrap px-1"
							style="border-bottom: 1px dotted; border-top: 1px dotted;"
						>
							{cells[2]}
						</td>
						<td
							class="border-l border-r text-center text-nowrap px-1"
							style="border-bottom: 1px dotted; border-top: 1px dotted;"
						>
							{cells[3]}
						</td>
						<td
							class="border-l border-r px-1"
							style="border-bottom: 1px dotted; border-top: 1px dotted;"
						>
							{cells[4]}
						</td>
						<td
							class="border-l border-r px-1"
							style="border-bottom: 1px dotted; border-top: 1px dotted;"
						>
							{cells[5]}
						</td>
						<td
							class="border-l border-r px-1"
							style="border-bottom: 1px dotted; border-top: 1px dotted;"
						>
							{cells[6]}
						</td>
						<td
							class="border-l border-r text-right text-nowrap px-1"
							style="border-bottom: 1px dotted; border-top: 1px dotted;"
						>
							{cells[7]}
						</td>
					</tr>
				{/each}
				<tr class="">
					<td
						class="border-l border-r text-center text-nowrap px-1 border-t border-b"
					>
					</td>
					<td
						class="border-l border-r text-center text-nowrap px-1 border-t border-b"
					>
					</td>
					<td
						class="border-l border-r text-center text-nowrap px-1 border-t border-b"
					>
					</td>
					<td
						class="border-l border-r text-center text-nowrap px-1 border-t border-b"
					>
					</td>
					<td class="border-l border-r px-1 text-center border-t border-b">
						{subtotal[0][4]}
					</td>
					<td class="border-l border-r px-1 border-t border-b"> </td>
					<td class="border-l border-r px-1 border-t border-b"> </td>
					<td
						class="border-l border-r text-right text-nowrap px-1 border-t border-b"
					>
						{subtotal[0][7]}
					</td>
				</tr>
			</tbody>
		{/each}
		<tbody>
			<tr class="">
				<td class="border-t"></td>
				<td class="border-t"></td>
				<td class="border-t"></td>
				<td class="border-t"></td>
				<td class="text-center border-l border-t border-b px-1">
					{footer[0][4]}
				</td>
				<td class="border-t border-b"></td>
				<td class="border-t border-b"></td>
				<td
					class="text-right border-l border-r border-t text-nowrap px-1"
					style="border-bottom: 3px double;"
				>
					{footer[0][7]}
				</td>
			</tr>
		</tbody>
	</table>
{/if}
