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

<input type="file" name="file" id="file" accept=".lss" onchange={setFile} />

{#if xml}
<h2>Analyse du fichier</h2>
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