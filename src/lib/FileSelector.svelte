<script>
	let file = $state(null);
	let xml = $state(null);
	let version = $state(null);
	let gameName = $state(null);
	let categoryName = $state(null);
	let runsNumber = $state(0);
	let segments = $state([]);
	
	const setFile = (e) => {
		file = e.target.files[0];
		const reader = new FileReader();
		reader.readAsText(file);
		reader.onload = (event) => {
			const data = event.target.result.toString();
			const domParser = new window.DOMParser();
			xml = domParser.parseFromString(data, "text/xml");
			version = xml.getElementsByTagName("Run")[0].getAttribute("version");
			gameName = xml.getElementsByTagName("GameName")[0].textContent;
			categoryName = xml.getElementsByTagName("CategoryName")[0].textContent;
			runsNumber = xml
				.getElementsByTagName("AttemptHistory")[0]
				.getElementsByTagName("Attempt").length;
			segments = Array.from(xml.getElementsByTagName("Segment"))
				.map((segment, index, array) => {
					return {
						name: segment.getElementsByTagName("Name")[0].textContent,
						bestRealTime: segment
							.getElementsByTagName("BestSegmentTime")[0]
							.textContent.trim()
							.split(" ")
							.pop(),
						runsNumber: segment.getElementsByTagName("Time").length,
					};
				})
				.map((v, i, a) => {
					return {
						...v,
						completedRunsNumber: a[i + 1] ? a[i + 1].runsNumber : 0,
					};
				})
				.map((v, i, a) => {
					return {
						...v,
						completedPercentage: v.runsNumber
							? Math.round((v.completedRunsNumber / v.runsNumber) * 100)
							: 0,
					};
				});
		};
	};
	</script>
	
	<label for="file">
		🔥 Choisir un fichier .lss
	</label>
	<input type="file" name="file" id="file" accept=".lss" onchange={setFile} />
	
	{#if xml}
	<h2>Récap du fichier</h2>
	Version : {version}<br>
	Jeu : {gameName}<br>
	Catégorie : {categoryName}<br>
	Nombre de runs : {runsNumber}<br>
	<hr>
	<table>
	  <thead>
		<tr>
		  <th>Nom du segment</th>
		  <th>Best Real Time</th>
		  <th>Nombre de runs</th>
		  <th>Seg terminés</th>
		  <th>Réussite %</th>
		</tr>
	  </thead>
	  <tbody>
		{#each segments as segment }
		<tr>
		  <td>
			{segment.name}
		  </td>
		  <td>
			{segment.bestRealTime}<br>
		  </td>
		  <td>
			{segment.runsNumber}
		  </td>
		  <td>
			{segment.completedRunsNumber}
		  </td>
		  <td>
			{segment.completedPercentage}%
		  </td>
		</tr>
		{/each}
	  </tbody>
	</table>
	{/if}
	<p>
	  Conçu par la grâce de Maxtodonte2
	</p>
	
	<style>
	:global(body) {
		background-color: #1a1a1a;
		color: #c0b6a8;
		font-family: 'Times New Roman', serif;
		margin: 2rem;
		background-image: linear-gradient(
			rgba(0, 0, 0, 0.5),
			rgba(0, 0, 0, 0.5)
		), url("data:image/svg+xml,%3Csvg width='52' height='26' viewBox='0 0 52 26' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%23333333' fill-opacity='0.4'%3E%3Cpath d='M10 10c0-2.21-1.79-4-4-4-3.314 0-6-2.686-6-6h2c0 2.21 1.79 4 4 4 3.314 0 6 2.686 6 6 0 2.21 1.79 4 4 4 3.314 0 6 2.686 6 6 0 2.21 1.79 4 4 4v2c-3.314 0-6-2.686-6-6 0-2.21-1.79-4-4-4-3.314 0-6-2.686-6-6zm25.464-1.95l8.486 8.486-1.414 1.414-8.486-8.486 1.414-1.414z' /%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
	}
	
	h2 {
		color: #8b0000;
		font-size: 2.5rem;
		text-shadow: 2px 2px 2px black;
		border-bottom: 2px solid #5a3e36;
		padding-bottom: 0.5rem;
	}
	
	table {
		width: 100%;
		border-collapse: collapse;
		margin: 2rem 0;
		background-color: #2e2e2e;
		box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
	}
	
	th {
		background-color: #5a3e36;
		color: #d4af37;
		padding: 1rem;
		text-align: left;
		font-variant: small-caps;
		letter-spacing: 1px;
	}
	
	td {
		padding: 0.8rem;
		border-bottom: 1px solid #4a4a4a;
	}
	
	tr:nth-child(even) {
		background-color: #363636;
	}
	
	tr:hover {
		background-color: #4a3a32;
		cursor: pointer;
	}
	
	input[type="file"] {
		display: none;
	}
	
	label {
		display: inline-block;
		background: #5a3e36;
		color: #c0b6a8;
		padding: 0.8rem 1.5rem;
		border: 1px solid #3a2e28;
		border-radius: 4px;
		cursor: pointer;
		font-family: inherit;
		transition: all 0.3s ease;
	}
	
	label:hover {
		background: #6a4e46;
		border-color: #4a3e38;
	}
	
	label:active {
		background: #4a3e36;
	}
	
	hr {
		border: 0;
		height: 1px;
		background-image: linear-gradient(
			to right,
			rgba(90, 62, 54, 0),
			rgba(90, 62, 54, 0.75),
			rgba(90, 62, 54, 0)
		);
		margin: 2rem 0;
	}
	
	p {
		text-align: center;
		font-style: italic;
		color: #5a3e36;
		margin-top: 2rem;
	}
	
	td:nth-child(5) {
		color: #d4af37;
		font-weight: bold;
	}
	
	td:nth-child(2) {
		color: #ffd700;
		font-family: monospace;
		text-shadow: 1px 1px 2px #8b4513;
		font-weight: bold;
	}
	
	tr:hover td:nth-child(2) {
		text-shadow: 0 0 5px rgba(255, 215, 0, 0.5);
	}
	</style>