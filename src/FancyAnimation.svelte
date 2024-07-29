<script>
	//- alpha and relative base displacement values
	let a = 0.5;
	let d = 0.05;

	//- get RGB values in proper format for feColorMatrix
	function toDec(num) {
		return +(num / 255).toFixed(2);
	}
</script>

{#snippet offset({ rgb, i, id })}
	{@const [r, g, b] = rgb.map((c) => toDec(c))}
	<feColorMatrix in={i} values="0 0 0 0 {r}  0 0 0 0 {g}  0 0 0 0 {b}  0 0 0 1 0"></feColorMatrix>
	<feBlend in="contline"></feBlend>
	<feDisplacementMap in2="basedmap" scale={id * d} xChannelSelector="R" yChannelSelector="G" result={`lyr${id}text`}> </feDisplacementMap>
{/snippet}

<svelte:head>
	<link rel="stylesheet" href="https://unpkg.com/tailwindcss@%5E3/src/css/preflight.css">
</svelte:head>

<main style={`--a: ${a}`}>
	<svg width="0" height="0" aria-hidden="true">
		<filter id="shadow" color-interpolation-filters="sRGB" primitiveUnits="objectBoundingBox">
			<!-- extract displacement map & limit it to -->
			<!-- original filter input element height-->
			<feColorMatrix values="0 -1 0 0  1 0 1 0  0 0 0 0  0 0 0 0  0 0 1 0" y="0" height="1"></feColorMatrix>
			<!-- extend it across the entire filter area around-->
			<!-- to get the displacement map-->
			<feTile result="basedmap"></feTile>
			<!-- extract text stroke-->
			<feColorMatrix in="SourceGraphic" values="0 0 0 0  1 0 0 0  0 1 0 0  0 0 1 {1 / a}  0 0 0 0" result="baseline"></feColorMatrix>
			<!-- get contrasting black stroke of offset copies-->
			<feColorMatrix in="SourceGraphic" values="0 0 0 0  0 0 0 0  0 0 0 0  0 0 0 {1 / a}  2 0 0 -2" result="contline"></feColorMatrix>
			<!-- extract base text fill-->
			<feColorMatrix in="SourceGraphic" values="0 0 0 0  1 0 0 0  0 1 0 0  0 0 1 0  0 {1 / (1 - a)} 0 0" result="basetext"></feColorMatrix>
			<!-- create the two offset copies given fill RGB values-->
			<!-- put the two copies together-->
			{@render offset({ rgb: [210, 20, 202], id: 1 })}
			{@render offset({ rgb: [20, 178, 193], i: "basetext", scale: 2, id: 2 })}
			<feBlend in2="lyr1text"></feBlend>
			<!-- with original outline-->
			<feBlend in2="baseline"></feBlend>
			<!-- and conditional on hover fill too-->
			<feBlend in2="basetext"></feBlend>
		</filter>
	</svg>
	<nav>
		<button class="my-button">Home</button>
		<a href="#">About</a>
		<a href="#">Portfolio</a>
		<a href="#">Team</a>
		<a href="#">Contact</a>
	</nav>
</main>

<style>
	@import url("https://fonts.googleapis.com/css?family=Poppins:900&display=swap");

	@property --hov {
		syntax: "<number>";
		initial-value: 0;
		inherits: true;
	}

	main,
	nav {
		display: grid;
	}

	main {
		height: 100%;
		place-content: center;
		margin: 0;
		background: #121212;
		font:
			900 6.5em/ 1.5 poppins,
			sans-serif !important;
		font-size: clamp(2em, 18vw, 9em) !important;
	}

	svg {
		position: fixed;
	}

	nav {
		grid-template-rows: repeat(5, 0.8lh);
		overflow: hidden;
	}

	a, button {
		--hov: 0;
		--g: calc((1 + var(--hov)) * 50%);
		overflow: hidden;
		padding: 0 0.5em;
		background: rgb(0%, var(--g), 0%);
		color: rgba(0%, var(--g), 100%, var(--hov));
		-webkit-text-stroke: clamp(2px, 0.015em, 6px) rgba(100%, var(--g), 0%, var(--a));
		text-decoration: none;
		isolation: isolate;
		filter: url(#shadow);
		transition: --hov 0.35s ease-out;

		&:hover,
		&:focus {
			--hov: 1;
		}

		&:focus {
			outline: none;
		}
	}

</style>
