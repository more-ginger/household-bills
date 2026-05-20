<script>
	import { resolve } from '$app/paths';
</script>

<section class="relative">
	<div class="absolute top-4 left-0 flex w-full justify-center">
		<div class="grid w-11/12 grid-cols-5 items-center gap-2 md:w-3/7 md:gap-6">
			<img src="logos/uol.png" alt="University of Liverpool" />
			<img src="logos/esrc.png" alt="ESRC" />
			<img
				class="w-[60%] place-self-center"
				src="logos/citizens_advice.png"
				alt="Citizens Advice"
			/>
			<img src="logos/uclab.png" alt="UCLAB" />
			<img src="logos/fhp.png" alt="FHP" />
		</div>
	</div>
	<div class="flex h-dvh">
		<div class="relative m-auto w-11/12 text-center md:w-3/7">
			<div class="absolute bottom-45 z-0 w-30 md:-right-10 md:bottom-25 md:w-40">
				<img src={resolve('/illustrations/webp/objects/bill.webp')} alt="" />
			</div>
			<div class="relative z-10">
				<h1 class="text-4xl font-bold md:text-6xl">Falling Behind on Household Bills</h1>
				<h2 class="mt-2 text-sm md:m-0 md:text-2xl">
					Risk Factors and Population Trends in the UK since 2009
				</h2>
			</div>
			<div class="mx-10 mt-6 text-xs md:mt-10 md:text-base">
				<p>
					Research by Maya Middleton-Welch <sup class="group relative cursor-help"
						>1<span
							class="pointer-events-none absolute bottom-full left-1/2 mb-1 -translate-x-1/2 rounded bg-white px-2 py-1 text-xs whitespace-nowrap text-primary-blue opacity-0 shadow transition-opacity group-hover:opacity-100"
							>University of Liverpool</span
						></sup
					>, Professor Mark Green
					<sup class="group relative cursor-help"
						>1<span
							class="pointer-events-none absolute bottom-full left-1/2 mb-1 -translate-x-1/2 rounded bg-white px-2 py-1 text-xs whitespace-nowrap text-primary-blue opacity-0 shadow transition-opacity group-hover:opacity-100"
							>University of Liverpool</span
						></sup
					>, Professor Alex Singleton<sup class="group relative cursor-help"
						>1<span
							class="pointer-events-none absolute bottom-full left-1/2 mb-1 -translate-x-1/2 rounded bg-white px-2 py-1 text-xs whitespace-nowrap text-primary-blue opacity-0 shadow transition-opacity group-hover:opacity-100"
							>University of Liverpool</span
						></sup
					>
				</p>
				<p>
					Digital briefing by Maya Middleton-Welch<sup class="group relative cursor-help"
						>1<span
							class="pointer-events-none absolute bottom-full left-1/2 mb-1 -translate-x-1/2 rounded bg-white px-2 py-1 text-xs whitespace-nowrap text-primary-blue opacity-0 shadow transition-opacity group-hover:opacity-100"
							>University of Liverpool</span
						></sup
					>
					and Dr Francesca Morini<sup class="group relative cursor-help"
						>2<span
							class="pointer-events-none absolute bottom-full left-1/2 mb-1 -translate-x-1/2 rounded bg-white px-2 py-1 text-xs whitespace-nowrap text-primary-blue opacity-0 shadow transition-opacity group-hover:opacity-100"
							>UCLAB, University of Applied Sciences Potsdam</span
						></sup
					>
				</p>
			</div>
		</div>
	</div>
	<div class="m-auto flex w-full flex-col px-4 md:my-30 md:w-5/7 md:flex-row md:px-0">
		<div class="mb-20 md:m-4 md:ml-2 md:w-2/5">
			<h3
				class="mb-2 flex justify-self-start font-epilogue leading-[1.3rem] font-normal text-white uppercase md:m-0"
			>
				Quick Overview
			</h3>
			<p class="pt-2 text-xl md:pr-10">
				We analysed data from a large nationally representative survey, Understanding Society, over
				the period 2009 to 2022. We identified trends and rates of demographic groups falling behind
				on household bills. Some individual and household characteristics are connected to the risk
				of falling behind on household bills.
			</p>
		</div>
		<div class="md:m-4 md:ml-2 md:w-3/5">
			<h3
				class="mb-2 flex justify-self-start font-epilogue leading-[1.3rem] font-normal text-white uppercase md:m-0"
			>
				Key Findings
			</h3>
			<div class="pt-8 md:pt-2">
				<p class="text-xl">Ethnic minority groups disproportionately affected since 2009</p>
				<div class="flex flex-wrap md:min-h-50">
					<img
						class="h-28 w-auto shrink-0 md:h-50"
						src={resolve('/illustrations/webp/humans/human1-blue.webp')}
						alt=""
					/>
					<img
						class="h-28 w-auto shrink-0 md:h-50"
						src={resolve('/illustrations/webp/humans/human10-darkblue.webp')}
						alt=""
					/>
					<img
						class="h-28 w-auto shrink-0 md:h-50"
						src={resolve('/illustrations/webp/humans/human6-blue.webp')}
						alt=""
					/>
					<img
						class="h-28 w-auto shrink-0 md:h-50"
						src={resolve('/illustrations/webp/humans/human7-darkblue.webp')}
						alt=""
					/>
					<img
						class="h-28 w-auto shrink-0 md:h-50"
						src={resolve('/illustrations/webp/humans/human8-blue.webp')}
						alt=""
					/>
				</div>
				<div class="my-10 flex items-center justify-center">
					<a
						href="?viz=trends#visualizations"
						class="flex w-fit cursor-pointer items-center gap-2 rounded-3xl bg-white p-2 px-4 text-primary-blue shadow"
						><img class="h-[1em] w-auto" src={resolve('/icons/arrow-down-skip.svg')} alt="" /> Jump to
						Risk Trends</a
					>
				</div>
			</div>
			<div class="pt-8 md:pt-2">
				<p class="text-xl">
					Key factors connected to an increased risk of being behind on bills include:
				</p>
				<div class="flex flex-wrap">
					<div class="flex w-1/2 flex-col md:w-1/4">
						<div class="flex h-45 items-center justify-center overflow-hidden">
							<img
								class="h-full w-full object-contain p-4"
								src={resolve('/illustrations/webp/humans/human12-darkblue-accent.webp')}
								alt=""
							/>
						</div>
						<p class="text-center text-xs">Disability</p>
					</div>
					<div class="flex w-1/2 flex-col md:w-1/4">
						<div class="flex h-45 items-center justify-center overflow-hidden">
							<img
								class="h-full w-full object-contain p-4"
								src={resolve('/illustrations/webp/humans/human4-darkblue-accent.webp')}
								alt=""
							/>
						</div>
						<p class="text-center text-xs">Caring for others in household</p>
					</div>
					<div class="flex w-1/2 flex-col md:w-1/4">
						<div class="flex h-45 items-center justify-center overflow-hidden">
							<img
								class="h-full w-full object-contain p-10 md:p-6"
								src={resolve('/illustrations/webp/objects/universal-credit.webp')}
								alt=""
							/>
						</div>
						<p class="text-center text-xs">Universal Credit</p>
					</div>
					<div class="flex w-1/2 flex-col md:w-1/4">
						<div class="flex h-45 items-center justify-center overflow-hidden">
							<img
								class="h-full w-full object-contain p-10 md:p-6"
								src={resolve('/illustrations/webp/objects/bill.webp')}
								alt=""
							/>
						</div>
						<p class="text-center text-xs">
							Higher percent of total household income from the welfare state
						</p>
					</div>
				</div>
				<div class="my-10 flex items-center justify-center">
					<a
						href="?viz=factors#visualizations"
						class="flex w-fit cursor-pointer items-center gap-2 rounded-3xl bg-white p-2 px-4 text-primary-blue shadow"
						><img class="h-[1em] w-auto" src={resolve('/icons/arrow-down-skip.svg')} alt="" /> Jump to
						Risk Factors</a
					>
				</div>
			</div>
		</div>
	</div>
	<div class="sticky bottom-10 left-0 w-full">
		<div
			class="m-auto grid w-fit grid-cols-1 justify-center rounded-xl bg-primary-blue/90 p-5 text-center"
		>
			<p class="mb-2 text-sm">Scroll down to read the report</p>
			<img
				src={resolve('/icons/arrow-down.svg')}
				class="h-5 w-auto justify-self-center"
				alt="Scroll down"
			/>
		</div>
	</div>
</section>
