<svelte:head>
	<link href="https://fonts.googleapis.com/css2?family=MedievalSharp&display=swap" rel="stylesheet">
</svelte:head>

<script>
let file = $state(null);
let xml = $state(null);
let version = $state(null);
let gameName = $state(null);
let categoryName = $state(null);
let runsNumber = $state(0);
let segments = $state([]);

const getSuccessClass = (percentage) => {
	if (Number(percentage) >= 75) return "success-high";
	if (Number(percentage) >= 50) return "success-medium";
	return "success-low";
};

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
<div class="header-container">
	<h1>1max2splits</h1>
	<label for="file">
	  🔥 Choisir un fichier .lss
	</label>
  </div>
<input type="file" name="file" id="file" accept=".lss" onchange={setFile} />
	
	{#if xml}
	<div class="header-section">
		<h2>Récap du fichier</h2>
		<div class="file-info">
			Version : {version}<br>
			Jeu : {gameName}<br>
			Catégorie : {categoryName}<br>
			Nombre de runs : {runsNumber}
		</div>
	</div>
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
		  <td class={getSuccessClass(segment.completedPercentage)}>
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
			background-color: #0a0a0a;
			color: #c0c0c0;
			font-family: 'Times New Roman', serif;
			padding: 2rem;
			min-height: 100vh;
		}
		h1 {
  font-size: 4rem;
  text-align: center;
  color: #c0a672;
  text-shadow: 3px 3px 2px black;
  margin: 2rem 0;
  font-family: 'MedievalSharp', cursive;
  letter-spacing: 0.2rem;
}

.header-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  margin-bottom: 3rem;
}
		h2 {
			color: #8b0000;
			font-size: 3.5rem;
			text-align: center;
			text-transform: uppercase;
			letter-spacing: 0.3rem;
			text-shadow: 3px 3px 2px black;
			margin: 2rem 0;
		}
	
		label {
			display: inline-block;
			background: #2a2a2a;
			border: 2px solid #756646;
			color: #c0a672;
			cursor: pointer;
			transition: all 0.3s ease;
			font-size: 1.4rem;
  padding: 1.5rem 3rem;
  margin-top: 1rem;
		}
	
		label:hover {
			background: #3a3a3a;
			border-color: #c0a672;
			box-shadow: 0 0 15px #c0a67244;
		}
	
		input[type="file"] {
			display: none;
		}
	
		.header-section {
			background: rgba(0, 0, 0, 0.7);
			padding: 2rem;
			border: 1px solid #756646;
			margin: 2rem 0;
			box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
		}
	
		.file-info {
			color: #c0a672;
			font-size: 1.1rem;
			line-height: 1.6;
			text-align: center;
		}
	
		table {
			width: 100%;
			border-collapse: collapse;
			margin: 2rem 0;
			background: rgba(20, 20, 20, 0.9);
		}
	
		th {
			background-color: #2a2a2a;
			color: #c0a672;
			padding: 1rem;
			border: 1px solid #756646;
			font-weight: bold;
			text-align: left;
		}
	
		td {
			padding: 1rem;
			border: 1px solid #3a3a3a;
			background-color: rgba(40, 40, 40, 0.5);
		}
	
		tr:nth-child(even) td {
			background-color: rgba(30, 30, 30, 0.5);
		}
	
		hr {
			border: 0;
			height: 1px;
			background-image: linear-gradient(to right, transparent, #756646, transparent);
			margin: 2rem 0;
		}
	
		p {
			text-align: center;
			color: #756646;
			font-style: italic;
			margin-top: 2rem;
		}
		.success-low {
		color: #8b0000;
	}
	
	.success-medium {
		color: #c0a672;
	}
	
	.success-high {
		color: #008b00;
	}
	</style>