<script>
	let url = "https://api.publicapis.org";
	let categories;
	let matches;
	let loading;
	let tag = "";
	let result;

	import { onMount } from "svelte";

	onMount(() => {
		fetch(`${url}/categories`)
			.then((res) => res.json())
			.then((data) => {
				categories = data;
			});
	});

	const search = () => {
		result = "";
		// let tag = document.getElementById("search").value;
		matches = categories.filter((cat) => {
			const regex = new RegExp(`^${tag}`, "gi");
			return cat.match(regex);
		});

		if (tag.length === 0) {
			matches = [];
		}
	};

	const getData = (match) => {
		tag = "";
		matches = undefined;
		let topic = match.split(" ")[0];

		loading = true;

		fetch(`${url}/entries?category=${topic}`)
			.then((res) => res.json())
			.then((data) => {
				result = data;
				loading = false;
			})
			.catch((err) => {
				console.error(err);
				loading = false;
			});
	};
</script>

<style>
</style>

<main>
	<div class="container mt-5">
		<div class="row">
			<div class="col-md-6 m-auto">
				<h3 class="text-center mb-3">Public API search</h3>
				<div class="form-group">
					<input
						type="text"
						id="search"
						class="form-control form-control-lg"
						placeholder="Search Topic"
						bind:value={tag}
						on:input={search} />
				</div>

				<div id="match-list">
					{#if matches}
						{#each matches as match}
							<div class="card card-body mb-1">
								<h4 on:click={getData(match)}>{match}</h4>
							</div>
						{/each}
					{/if}
				</div>
				{#if result}
					<p>Total results: {result.count}</p>
					{#each result.entries as api}
						<div class="card border-primary mb-3">
							<div class="card-header">{api.API}</div>
							<div class="card-body">
								<p class="card-text">{api.Description}</p>
								<table class="table table-bordered">
									<tbody>
										<tr>
											<td>Auth</td>
											<td>{api.Auth}</td>
										</tr>
										<tr>
											<td>CORS</td>
											<td>{api.Cors}</td>
										</tr>
										<tr>
											<td>HTTPS</td>
											<td>{api.HTTPS}</td>
										</tr>
									</tbody>
								</table>
								<a
									href={api.Link}
									target="_blank"
									class="btn btn-success">Go to API</a>
							</div>
						</div>
						<hr />
					{/each}
				{/if}
			</div>
		</div>
	</div>
	<center>
		{#if loading}
			<div class="spinner-border" role="status">
				<span class="sr-only">Loading...</span>
			</div>
		{/if}
	</center>
</main>
