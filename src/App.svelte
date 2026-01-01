<script>
	import { onMount } from "svelte";
	const XLSX = globalThis.XLSX;
	const multilingual = { en: {}, th: {} };

	let user = $state({ lang: "en" });
	let spread = $state([
		["", "", "", ""],
		["", "", "", ""],
		["", "", "", ""],
		["", "", "", ""],
	]);
	let columnTypes = $state([]);

	let report = $derived.by(() => {
		let data = [];
		let footer = [];
		spread[0].forEach((colName, colindex) => {
			if (["Total"].includes(columnTypes[colindex])) {
				footer[colindex] = 0;
			}
		});
		spread.slice(1).forEach((row, rowindex) => {
			data[rowindex] = [];
			row.forEach((col, colindex) => {
				if (["Total", "Amount"].includes(columnTypes[colindex])) {
					const amount = Number(col.replace(/[^0-9.-]/g, ""));
					data[rowindex][colindex] = amount;
					footer[colindex] += amount;
				} else {
					data[rowindex][colindex] = col;
				}
			});
		});
		return { data, footer };
	});

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
		return new Date(value).toLocaleDateString(user.lang, {
			day: "numeric",
			month: "short",
			year: "numeric",
			...option,
		});
		// month: "long",
		// weekday: "long",
	}
	function monetize(value, option) {
		value = Number(value);
		if (value == 0) {
			return "";
		} else {
			return Number(value).toLocaleString(user.lang, {
				minimumFractionDigits: 2,
				maximumFractionDigits: 2,
				...option,
			});
		}
		// let europeanString = "1.500,00";
		// let number = parseFloat(europeanString.replace(/\./g, '').replace(',', '.'));
	}
	function upload(file) {
		file.arrayBuffer().then((rawTrans) => {
			const fileTrans = XLSX.read(rawTrans, { cellDates: true });
			const sheetName = fileTrans.SheetNames[0];
			const worksheet = fileTrans.Sheets[sheetName];
			let aoa = XLSX.utils.sheet_to_json(worksheet, { header: 1 });
			spread = aoa;
		});
	}

	onMount(() => {
		const params = new URLSearchParams(location.search);
		const lang = params.get("lang");
		if (multilingual[lang]) {
			user.lang = lang;
		}
	});
</script>

<div class="print:hidden flex flex-wrap justify-center gap-4 p-4 select-none">
	<div class="">
		<details>
			<summary
				class="list-none cursor-pointer text-violet-600 font-semibold text-lg"
			>
				Upload
			</summary>
			<input
				class="border"
				type="file"
				accept="xlsx"
				onchange={(e) => {
					const file = e.currentTarget.files[0];
					upload(file);
				}}
			/>
		</details>
	</div>
	<div class="">
		<details>
			<summary
				class="list-none cursor-pointer text-violet-600 font-semibold text-lg"
			>
				input
			</summary>
			<textarea
				class="border"
				placeholder="Paste copied data here"
				onchange={(e) => {
					const value = e.currentTarget.value;
					arrize(value);
				}}
			></textarea>
		</details>
	</div>
	<div class="">
		<button
			class="cursor-pointer text-violet-600 font-semibold text-lg"
			onclick={() => {
				print();
			}}
		>
			Print
		</button>
	</div>
	<div class="">
		<select
			class="text-violet-600 font-semibold"
			value={user.lang}
			onchange={(e) => {
				const locale = e.currentTarget.value;
				user.lang = locale;
			}}
		>
			{#each Object.keys(multilingual) as locale}
				<option value={locale}>{locale.toUpperCase()}</option>
			{/each}
		</select>
	</div>
</div>

<table class="mx-auto">
	<tbody class="">
		<tr class="">
			{#each spread[0] as colName, colindex}
				<td class="border px-1">
					<label class="select-none print:hidden">
						<select
							class="field-sizing-content"
							onchange={(e) => {
								const value = e.currentTarget.value;
								columnTypes[colindex] = value;
							}}
						>
							<option value="">&nbsp;</option>
							{#each ["Date", "Amount", "Total",'no-wrap'] as type}
								<option value={type}>{type}</option>
							{/each}
						</select>
					</label>
					{colName}
				</td>
			{/each}
		</tr>
		{#each report.data as cells, rowindex}
			<tr class="">
				{#each cells as value, colindex}
					{@const columnType = columnTypes[colindex]}
					<td
						class="border px-1"
						class:text-right={["Amount", "Total"].includes(
							columnType,
						)}
						class:text-center={["Date"].includes(columnType)}
						class:text-nowrap={['Date','no-wrap'].includes(columnType)}
					>
						{#if ["Date"].includes(columnType)}
							{datize(value)}
						{:else if ["Amount", "Total"].includes(columnType)}
							{monetize(value)}
						{:else}
							{value}
						{/if}
					</td>
				{/each}
			</tr>
		{/each}
		{#if columnTypes.includes("Total")}
			<tr class="">
				{#each spread[0] as colName, colindex}
					<td
						class={["Total"].includes(columnTypes[colindex])
							? "text-right border px-1"
							: ""}
					>
						{#if ["Total"].includes(columnTypes[colindex])}
							{monetize(report.footer[colindex])}
						{:else}
							{""}
						{/if}
					</td>
				{/each}
			</tr>
		{/if}
	</tbody>
</table>
