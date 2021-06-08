<script lang="ts">
	import Team from '@bleckert/football-simulator/Team';
	import { Position } from '@bleckert/football-simulator/enums/Position';
	import type { TeamReport } from '@bleckert/football-simulator/types/Report';

	export let data: Team;
	export let report: TeamReport;

	const rating = Object.entries(data.rating());
	const shortNames = {
		goalkeeping: 'GK',
		attack: 'ATT',
		defense: 'DEF'
	};

	console.log(report);
</script>

<div class="team">
	<h1>{data.name}</h1>
	<ul class="stats">
		{#each rating as [key, value]}
			<li class="stat">
				<strong>{shortNames[key]}</strong>
				<span>{Math.round(value)}</span>
			</li>
		{/each}
	</ul>
	<div class="report">
		<table>
			<tbody>
				<tr>
					<th>Possession</th>
					<td>{Number.isNaN(report.possession) ? 0 : Math.round(report.possession * 100)}%</td>
				</tr>
				<tr>
					<th>Shots</th>
					<td>{report.shots}</td>
				</tr>
				<tr>
					<th>Shots on goal</th>
					<td>{report.shotsOnGoal}</td>
				</tr>
			</tbody>
		</table>
	</div>
	<table class="players">
		<thead>
			<tr>
				<th>#</th>
				<th>Name</th>
				<th>Position</th>
			</tr>
		</thead>
		<tbody>
			{#each data.players as player}
				<tr>
					<td>{player.info.number}</td>
					<td><span class="name">{player.info.name}</span></td>
					<td>{Position[player.position]}</td>
				</tr>
			{/each}
		</tbody>
	</table>
</div>

<style>
	.team {
		display: flex;
		flex-direction: column;
		max-width: 600px;
		width: 100%;
		padding: var(--gutter);
	}

	h1 {
		text-align: center;
	}

	.report {
		margin-top: var(--gutter-y);
		background: var(--brand-alternate-color);
	}

	.report table {
		margin: 0;
	}

	.stats {
		display: flex;
		justify-content: center;
		list-style: none;
		margin: var(--gutter-y) 0 0;
		padding: 0;
	}

	.stat {
		display: flex;
		flex-direction: column;
		padding: 0 calc(var(--gutter) / 2);
		text-align: center;
	}

	table {
		width: 100%;
		border-collapse: collapse;
		margin-top: var(--gutter-y);
	}

	th {
		text-transform: uppercase;
		letter-spacing: 1px;
		font-size: var(--font-size-small);
		text-align: left;
		padding: calc(var(--gutter) / 4) calc(var(--gutter) / 2);
	}

	td {
		padding: calc(var(--gutter) / 4) calc(var(--gutter) / 2);
	}

	td:first-child {
		width: 1%;
		white-space: nowrap;
	}

	td:last-child,
	th:last-child {
		text-align: right;
	}

	.name {
		display: block;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}
</style>
