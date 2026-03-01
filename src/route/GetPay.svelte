<script>
	let { spread } = $props();

	const body = $derived.by(() => {
		let result = Object.groupBy(spread.slice(3, -2), (cells) => {
			return cells[0];
		});

		return Object.entries(result);
	});
	const header = $derived(spread.slice(0, 3));
	const footer = $derived(spread.slice(-2));
</script>

{#if body.length > 0 && header.length > 0 && footer.length > 0}
	<table class="w-full text-sm">
		<thead class="">
			<tr class="">
				<td class="text-center" colspan="8">
					{header[0][0]}
				</td>
			</tr>
			<tr class="text-center">
				<td class="border-l border-r border-t border-b px-1">
					{header[1][0]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[1][1]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[1][2]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[1][3]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[1][4]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[1][5]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[1][6]}
				</td>
				<td class="border-l border-r border-t border-b px-1">
					{header[1][7]}
				</td>
			</tr>
		</thead>
		<tbody class="">
			<tr class="">
				<td
					class="border-l border-r border-t text-center text-nowrap px-1"
					style="border-bottom: 1px dotted;"
				></td>
				<td
					class="border-l border-r border-t text-center text-nowrap px-1"
					style="border-bottom: 1px dotted;"
				>
				</td>
				<td
					class="border-l border-r border-t text-center text-nowrap px-1"
					style="border-bottom: 1px dotted;"
				>
				</td>
				<td
					class="border-l border-r border-t text-center text-nowrap px-1"
					style="border-bottom: 1px dotted;"
				>
				</td>
				<td
					class="border-l border-r border-t px-1 text-center"
					style="border-bottom: 1px dotted;"
				>
					{header[2][4]}
				</td>
				<td
					class="border-l border-r border-t text-right text-nowrap px-1"
					style="border-bottom: 1px dotted;"
				>
				</td>
				<td
					class="border-l border-r border-t text-right text-nowrap px-1"
					style="border-bottom: 1px dotted;"
				>
				</td>
				<td
					class="border-l border-r border-t text-right text-nowrap px-1"
					style="border-bottom: 1px dotted;"
				>
					{header[2][7]}
				</td>
			</tr>
		</tbody>
		{#each body as [group, data]}
			<tbody class="break-inside-avoid-page">
				{#each data as cells, rowindex}
					{@const isLastRow = rowindex == data.length - 1}
					<tr class={isLastRow ? "" : "font-extralight"}>
						<td
							class="border-l border-r text-center text-nowrap px-1 {isLastRow
								? 'border-t border-b'
								: ''}"
							style={isLastRow
								? ""
								: "border-bottom: 1px dotted; border-top: 1px dotted;"}
						>
							{#if rowindex == 0 || (data[rowindex + 1] && data[rowindex + 1][0] != cells[0])}
								{cells[0]}
							{/if}
						</td>
						<td
							class="border-l border-r text-center text-nowrap px-1 {isLastRow
								? 'border-t border-b'
								: ''}"
							style={isLastRow
								? ""
								: "border-bottom: 1px dotted; border-top: 1px dotted;"}
						>
							{cells[1]}
						</td>
						<td
							class="border-l border-r text-center text-nowrap px-1 {isLastRow
								? 'border-t border-b'
								: ''}"
							style={isLastRow
								? ""
								: "border-bottom: 1px dotted; border-top: 1px dotted;"}
						>
							{cells[2]}
						</td>
						<td
							class="border-l border-r text-center text-nowrap px-1 {isLastRow
								? 'border-t border-b'
								: ''}"
							style={isLastRow
								? ""
								: "border-bottom: 1px dotted; border-top: 1px dotted;"}
						>
							{cells[3]}
						</td>
						<td
							class="border-l border-r px-1 {isLastRow
								? 'text-center border-t border-b'
								: ''}"
							style={isLastRow
								? ""
								: "border-bottom: 1px dotted; border-top: 1px dotted;"}
						>
							{cells[4]}
						</td>
						<td
							class="border-l border-r text-right text-nowrap px-1 {isLastRow
								? 'border-t border-b'
								: ''}"
							style={isLastRow
								? ""
								: "border-bottom: 1px dotted; border-top: 1px dotted;"}
						>
							{cells[5]}
						</td>
						<td
							class="border-l border-r text-right text-nowrap px-1 {isLastRow
								? 'border-t border-b'
								: ''}"
							style={isLastRow
								? ""
								: "border-bottom: 1px dotted; border-top: 1px dotted;"}
						>
							{cells[6]}
						</td>
						<td
							class="border-l border-r text-right text-nowrap px-1 {isLastRow
								? 'border-t border-b'
								: ''}"
							style={isLastRow
								? ""
								: "border-bottom: 1px dotted; border-top: 1px dotted;"}
						>
							{#if rowindex == 0 || (data[rowindex + 1] && !data[rowindex + 1][7])}
								{cells[7]}
							{/if}
						</td>
					</tr>
				{/each}
			</tbody>
		{/each}
		<tbody>
			<tr class="">
				<td class="border-t border-l"></td>
				<td class="border-t"></td>
				<td class="border-t"></td>
				<td class="border-t"></td>
				<td class="text-center border-l border-r border-t border-b px-1">
					{footer[0][4]}
				</td>
				<td
					class="text-right border-l border-r border-t border-b text-nowrap px-1"
				>
					{footer[0][5]}
				</td>
				<td
					class="text-right border-l border-r border-t border-b text-nowrap px-1"
				>
					{footer[0][6]}
				</td>
				<td class="border-t border-r"></td>
			</tr>
			<tr class="">
				<td class="border-t"></td>
				<td class="border-t"></td>
				<td class="border-t"></td>
				<td class="border-t"></td>
				<td
					class="text-center border-l border-r border-t px-1"
					style="border-bottom: 3px double;"
				>
					{footer[1][4]}
				</td>
				<td
					class="text-right border-l border-r border-t text-nowrap px-1"
					style="border-bottom: 3px double;"
				>
					{footer[1][5]}
				</td>
				<td
					class="text-right border-l border-r border-t text-nowrap px-1"
					style="border-bottom: 3px double;"
				>
					{footer[1][6]}
				</td>
				<td class="border-t"></td>
			</tr>
		</tbody>
	</table>
{/if}
