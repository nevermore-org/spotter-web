<script lang="ts">
    import Autocomplete from "@smui-extra/autocomplete";
	import CircularProgress from '@smui/circular-progress';
	import Button, { Label } from "@smui/button";
	import LOADING_MESSAGES from "../enum/LoadingMessages";
	import Select, { Option } from "@smui/select";

    let gw2mapName: string;
	let gw2mapURL: string;	
	let gw2mapNames: any[];
	let inProgress: boolean = false;
	let value = 'bmap';
	let progressError = "No map generated so far!";
	let searchBlockMaxHeight = 25;

	const genModes = [{value: "bmap", name: "Only base map"}, {value: "imap", name: "BMap + icons"}, {value: "fmap", name: "BMap + icons + labels"}];


    const addOutline = (id: string) => {
        const mapImage = document.getElementById(id);
        mapImage.style.outline = `solid #C1B9A8`;
        mapImage.style.outlineOffset = "10px";
        mapImage.style.borderRadius = "0.5rem";
	}

	const fadeAwayTitle = () => {
		const titleElement = document.getElementById("title");
		if (titleElement === null) {return};

		setTimeout(() => titleElement.remove(), 550);
		titleElement.style.opacity = titleElement.style.padding = titleElement.style.fontSize = "0";
		searchBlockMaxHeight = 45;
	}

	const randomizeLoadingMessage = () => {
		return LOADING_MESSAGES[Math.floor(Math.random() * LOADING_MESSAGES.length)];
	}

	const getMaps = async () => {
		try {
			const returnValue = await fetch(`/api/maps`);
			const response = await returnValue.json();
			gw2mapNames = response.map( gw2map => gw2map.name).sort();
		} catch (err) {
			console.error(err);
		}
	}

	async function renderMap() {
		if (!gw2mapName) {return};
		inProgress = true;
		try {
			const returnValue = await fetch(`/api/map-gen/?mode=${value}&name=${gw2mapName}`);
			const response = await returnValue.json();
			gw2mapURL = response.data[0].mapURL;

		} catch (err) {
			progressError = `Couldn't generate the ${value} of ${gw2mapName}`;
			gw2mapURL = undefined;
		}
		inProgress = false;
		fadeAwayTitle();
	}
	$: getMaps();

</script>
<span>
	<h1 id="title">GW2 Hi-Res Maps</h1>
</span>

<div id="search-block" style="--mdc-menu-max-height: {searchBlockMaxHeight}em">
    <Autocomplete
        options={gw2mapNames}
        bind:value={gw2mapName}
        label="Map"
        style="padding: 10px"
    />

    <div class="columns margins" style="justify-content: flex-end;">
        <div style="padding: 10px">
            <Select bind:value label="Render-mode">
				{#each genModes as mode}
					<Option value={mode.value}>{mode.name}</Option>
				{/each}
            </Select>
        </div>
    </div>

    <Button on:click={renderMap} variant="outlined">
        <Label>Render map</Label>
    </Button>
</div>

<div id="output-field" class="info-text">
    {#if gw2mapURL && !inProgress}
        <img id="rendered-map" src={gw2mapURL} alt="base map" on:load={() => addOutline("rendered-map")}>
    {:else if inProgress}
        <div id="progress">
            <div id="progress-circle"><CircularProgress style="height: 48px; width: 48px;" indeterminate /></div>
            <div id="progress-text" style="padding: 10px">{randomizeLoadingMessage()}</div>
        </div>
    {:else}<div style="color: #BC4749">{progressError}</div>{/if}
</div>


<style>
    .info-text {
		font-family: "Palanquir", sans-serif;
	}

	#title {
		transition: all 0.55s ease-in-out;
	}

    #rendered-map {
		max-width: 50%;
		max-height: 40em;
	}

	#search-block {
		display: flex;
		padding-bottom: 40px;
		flex-direction: row;
		justify-content: center;
		align-items: baseline;
	}

	#progress-circle {
		display: flex; 
		justify-content: center;
	}

</style>