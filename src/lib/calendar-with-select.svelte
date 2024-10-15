<script lang="ts">
	import { Calendar, type WithoutChildrenOrChild } from 'bits-ui';
	// view the source of the Bits UI calendar here: https://github.com/huntabyte/bits-ui/blob/next/packages/bits-ui/src/lib/bits/calendar/components/calendar.svelte
	import { DateFormatter, getLocalTimeZone } from '@internationalized/date';

	let {
		value = $bindable(),
		placeholder = $bindable(),
		weekdayFormat = 'short',
		...restProps
	}: WithoutChildrenOrChild<Calendar.RootProps> = $props();

	const monthOptions = [
		'January',
		'February',
		'March',
		'April',
		'May',
		'June',
		'July',
		'August',
		'September',
		'October',
		'November',
		'December'
	].map((month, i) => ({ value: i + 1, label: month }));

	const monthFmt = new DateFormatter('en-US', {
		month: 'long'
	});

	const defaultMonth = $derived(
		placeholder
			? {
					value: placeholder.month,
					label: monthFmt.format(placeholder.toDate(getLocalTimeZone()))
				}
			: undefined
	);

	// destructuring + disc. unions does not play well :/
	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	const rest = $derived(restProps) as any;
</script>

<Calendar.Root bind:value bind:placeholder {weekdayFormat} {...rest}>
	{#snippet children({ months, weekdays })}
		<Calendar.Header>
			<Calendar.Heading>
				<select value={defaultMonth?.value}>
					{#each monthOptions as { value, label }}
						<option {value}>{label}</option>
					{/each}
				</select>
			</Calendar.Heading>
		</Calendar.Header>
		<div>
			{#each months as month}
				<Calendar.Grid>
					<Calendar.GridHead>
						<Calendar.GridRow>
							{#each weekdays as weekday}
								<Calendar.HeadCell>
									{weekday.slice(0, 2)}
								</Calendar.HeadCell>
							{/each}
						</Calendar.GridRow>
					</Calendar.GridHead>
					<Calendar.GridBody>
						{#each month.weeks as weekDates}
							<Calendar.GridRow>
								{#each weekDates as date}
									<Calendar.Cell {date} month={month.value}>
										<Calendar.Day />
									</Calendar.Cell>
								{/each}
							</Calendar.GridRow>
						{/each}
					</Calendar.GridBody>
				</Calendar.Grid>
			{/each}
		</div>
	{/snippet}
</Calendar.Root>
