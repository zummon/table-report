<script>
	import { onMount } from "svelte";
	const routes = ["Get Pay", "File Doc"];
	const pasteStructures = [
		"title\ndate, column name x4, debit, credit, left over\ncarry 0\n12 xx xx xx xx 10 0 0\n...",
		"",
	];

	let spread = $state([
		["", "", "", ""],
		["", "", "", ""],
		["", "", "", ""],
		["", "", "", ""],
	]);
	let route = $state(0);

	function arrize(str) {
		let result = [];
		str.split("\n").forEach((row, rowindex) => {
			result[rowindex] = [];
			row.split("\t").forEach((col, colindex) => {
				result[rowindex][colindex] = col;
			});
		});
		spread = result;
	}
	function datize(value, option) {
		return new Date(value).toLocaleDateString(undefined, {
			month: "short",
			weekday: undefined,
			day: "numeric",
			year: "numeric",
			...option,
		});
	}
	function monetize(value, option) {
		value = Number(value);
		if (value == 0) {
			return "";
		} else {
			return Number(value).toLocaleString(undefined, {
				minimumFractionDigits: 2,
				maximumFractionDigits: 2,
				...option,
			});
		}
	}
	function numerize(str) {
		// gemini.google
		const cleanStr = str.replace(/[^\d.,-]/g, "");

		const lastDotIndex = cleanStr.lastIndexOf(".");
		const lastCommaIndex = cleanStr.lastIndexOf(",");

		if (lastCommaIndex > lastDotIndex) {
			const standardized = cleanStr.replace(/\./g, "").replace(",", ".");
			return parseFloat(standardized);
		}
		const standardized = cleanStr.replace(/,/g, "");
		return parseFloat(standardized);
	}

	onMount(() => {
		const params = new URLSearchParams(location.search);
	});
</script>

<div
	class="print:hidden flex flex-wrap justify-center gap-3 px-6 py-4 bg-white/80 backdrop-blur border-b border-neutral-200 shadow-sm sticky top-0 z-10 select-none"
>
	<div class="">
		<button
			class="inline-flex items-center gap-2 px-4 py-2 rounded-lg border border-emerald-200 bg-emerald-50 text-emerald-700 font-semibold cursor-pointer transition-all duration-150 hover:bg-emerald-100 hover:border-emerald-300 hover:shadow-sm active:scale-95 active:bg-emerald-200"
			onclick={() => {
				print();
			}}
		>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				viewBox="0 0 24 24"
				fill="currentColor"
				class="size-6"
			>
				<path
					fill-rule="evenodd"
					d="M7.875 1.5C6.839 1.5 6 2.34 6 3.375v2.99c-.426.053-.851.11-1.274.174-1.454.218-2.476 1.483-2.476 2.917v6.294a3 3 0 0 0 3 3h.27l-.155 1.705A1.875 1.875 0 0 0 7.232 22.5h9.536a1.875 1.875 0 0 0 1.867-2.045l-.155-1.705h.27a3 3 0 0 0 3-3V9.456c0-1.434-1.022-2.7-2.476-2.917A48.716 48.716 0 0 0 18 6.366V3.375c0-1.036-.84-1.875-1.875-1.875h-8.25ZM16.5 6.205v-2.83A.375.375 0 0 0 16.125 3h-8.25a.375.375 0 0 0-.375.375v2.83a49.353 49.353 0 0 1 9 0Zm-.217 8.265c.178.018.317.16.333.337l.526 5.784a.375.375 0 0 1-.374.409H7.232a.375.375 0 0 1-.374-.409l.526-5.784a.373.373 0 0 1 .333-.337 41.741 41.741 0 0 1 8.566 0Zm.967-3.97a.75.75 0 0 1 .75-.75h.008a.75.75 0 0 1 .75.75v.008a.75.75 0 0 1-.75.75H18a.75.75 0 0 1-.75-.75V10.5ZM15 9.75a.75.75 0 0 0-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 0 0 .75-.75V10.5a.75.75 0 0 0-.75-.75H15Z"
					clip-rule="evenodd"
				/>
			</svg>
			Print
		</button>
	</div>
	{#each routes as nav, index}
		<div class="">
			<button
				class="inline-flex items-center px-4 py-2 rounded-lg border font-semibold cursor-pointer transition-all duration-150 active:scale-95 border-sky-200 bg-sky-50 text-sky-700 hover:bg-sky-100 hover:border-sky-300 hover:shadow-sm active:bg-sky-200 disabled:bg-white"
				disabled={route === index}
				onclick={() => {
					route = index;
				}}
			>
				{nav}
			</button>
		</div>
	{/each}
</div>

<div class="print:hidden">
	<div class="max-w-2xl mx-auto mt-4">
		<label class="inline-flex items-center gap-2 mb-2 font-semibold text-neutral-700">
			<span class="inline-block">
				<svg
					xmlns="http://www.w3.org/2000/svg"
					fill="none"
					viewBox="0 0 24 24"
					stroke-width="1.5"
					stroke="currentColor"
					class="size-6"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M3.75 9.776c.112-.017.227-.026.344-.026h15.812c.117 0 .232.009.344.026m-16.5 0a2.25 2.25 0 0 0-1.883 2.542l.857 6a2.25 2.25 0 0 0 2.227 1.932H19.05a2.25 2.25 0 0 0 2.227-1.932l.857-6a2.25 2.25 0 0 0-1.883-2.542m-16.5 0V6A2.25 2.25 0 0 1 6 3.75h3.879a1.5 1.5 0 0 1 1.06.44l2.122 2.12a1.5 1.5 0 0 0 1.06.44H18A2.25 2.25 0 0 1 20.25 9v.776"
					/>
				</svg>
			</span>
			<span> Paste copied data here </span>
		</label>

		<textarea
			class="w-full min-h-48 rounded-xl border border-neutral-200 bg-white px-4 py-3 text-neutral-800 placeholder-neutral-400 shadow-sm transition-all duration-150 focus:outline-none focus:ring-2 focus:ring-blue-400 focus:border-blue-400 hover:border-neutral-300 resize-y"
			placeholder={pasteStructures[route]}
			onchange={(e) => {
				const value = e.currentTarget.value;
				arrize(value);
			}}
		></textarea>
	</div>
</div>

<div class="p-4 print:p-0">
	{#if route == 0}
		{@const report = spread.slice(2).reduce(
			(prev, curr) => {
				let cells = [];
				curr.forEach((value, colindex) => {
					cells[colindex] = value;
				});
				prev.data.push(cells);

				return prev;
			},
			{ data: [] },
		)}
		<table class="w-full">
			<tbody class="">
				<tr class="">
					<td class="text-center" colspan="8">
						{spread[0][0]}
					</td>
				</tr>
				<tr class="text-center">
					<td class="border-l border-r border-t border-b px-1">
						{spread[1][0]}
					</td>
					<td class="border-l border-r border-t border-b px-1">
						{spread[1][1]}
					</td>
					<td class="border-l border-r border-t border-b px-1">
						{spread[1][2]}
					</td>
					<td class="border-l border-r border-t border-b px-1">
						{spread[1][3]}
					</td>
					<td class="border-l border-r border-t border-b px-1">
						{spread[1][4]}
					</td>
					<td class="border-l border-r border-t border-b px-1">
						{spread[1][5]}
					</td>
					<td class="border-l border-r border-t border-b px-1">
						{spread[1][6]}
					</td>
					<td class="border-l border-r border-t border-b px-1">
						{spread[1][7]}
					</td>
				</tr>
				{#each report.data as cells, rowindex}
					<tr class="">
						<td
							class="border-l border-r border-t border-b text-center text-nowrap px-1"
						>
							{cells[0]}
						</td>
						<td
							class="border-l border-r border-t border-b text-center text-nowrap px-1"
						>
							{cells[1]}
						</td>
						<td
							class="border-l border-r border-t border-b text-center text-nowrap px-1"
						>
							{cells[2]}
						</td>
						<td
							class="border-l border-r border-t border-b text-center text-nowrap px-1"
						>
							{cells[3]}
						</td>
						<td class="border-l border-r border-t border-b px-1">
							{cells[4]}
						</td>
						<td
							class="border-l border-r border-t border-b text-right text-nowrap px-1"
						>
							{cells[5]}
						</td>
						<td
							class="border-l border-r border-t border-b text-right text-nowrap px-1"
						>
							{cells[6]}
						</td>
						<td
							class="border-l border-r border-t border-b text-right text-nowrap px-1"
						>
							{cells[7]}
						</td>
					</tr>
				{/each}
				<tr class="">
					<td class="border-t"></td>
					<td class="border-t"></td>
					<td class="border-t"></td>
					<td class="border-t"></td>
					<td class="text-center border-l border-r border-t border-b px-1">
						รวมทั้งเดือน
					</td>
					<td
						class="text-right border-l border-r border-t border-b text-nowrap px-1"
					>
						{monetize(0)}
					</td>
					<td
						class="text-right border-l border-r border-t border-b text-nowrap px-1"
					>
						{monetize(0)}
					</td>
					<td class="border-t"></td>
				</tr>
			</tbody>
		</table>
	{:else if route == 1}
		<table></table>
	{:else}{/if}
</div>
