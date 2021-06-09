<script lang="ts">
	import Team from '@bleckert/football-simulator/Team';
	import { Position } from '@bleckert/football-simulator/enums/Position';
	import Game from '@bleckert/football-simulator/Game';
	import Reporter from '@bleckert/football-simulator/Reporter';
	import Commentator from '@bleckert/football-simulator/Commentator';
	import createPlayer from '$lib/createPlayer';
	import TeamComponent from '$lib/Team.svelte';
	import { GameEvent } from '@bleckert/football-simulator/types/GameEvent';
	import type { Report } from '@bleckert/football-simulator/types/Report';

	const homePlayers = [
		createPlayer(Position.GK),
		createPlayer(Position.LB),
		createPlayer(Position.LCB),
		createPlayer(Position.RCB),
		createPlayer(Position.RB),
		createPlayer(Position.LM),
		createPlayer(Position.LCM),
		createPlayer(Position.RCM),
		createPlayer(Position.RM),
		createPlayer(Position.LF),
		createPlayer(Position.RF)
	];

	const awayPlayers = [
		createPlayer(Position.GK),
		createPlayer(Position.LB),
		createPlayer(Position.LCB),
		createPlayer(Position.RCB),
		createPlayer(Position.RB),
		createPlayer(Position.LCM),
		createPlayer(Position.CM),
		createPlayer(Position.RCM),
		createPlayer(Position.LW),
		createPlayer(Position.CF),
		createPlayer(Position.RW)
	];

	const homeTeam = new Team(true, 'Juventus', homePlayers);
	const awayTeam = new Team(false, 'Milan', awayPlayers);
	const commentator = new Commentator();
	const game = new Game(homeTeam, awayTeam, commentator);

	let homeScore = game.engine.gameInfo.homeGoals;
	let awayScore = game.engine.gameInfo.awayGoals;
	let matchMinute = game.engine.gameInfo.matchMinute;
	let started = false;

	function startGame() {
		started = true;
		game.start();
	}

	let localEvents: GameEvent[] = [];
	let report: Report = new Reporter(localEvents).getReport();

	game.on('event', (data: GameEvent) => {
		matchMinute = data.gameInfo.matchMinute;
		homeScore = data.gameInfo.homeGoals;
		awayScore = data.gameInfo.awayGoals;

		localEvents.push(data);
		localEvents = localEvents;
		report = new Reporter(localEvents).getReport();
	});
</script>

<div class="scoreboard">
	<span class="match-minute" on:click={startGame}>
		{#if started}
			'{matchMinute}
		{/if}
		{#if !started}
			Start
		{/if}
	</span>
	<div class="scores">
		<span>{homeScore}</span>
		<span>-</span>
		<span>{awayScore}</span>
	</div>
	{#if report.scoreSheet && report.scoreSheet.length}
		<div class="score-sheet">
			{#each report.scoreSheet as item}
				<div class="score-sheet__item" class:away={!item.team.home}>
					<div>
						<div class="score-sheet__item__name">{item.goalScorer.info.name}</div>
						<div>{item.matchMinute}'</div>
					</div>
				</div>
			{/each}
		</div>
	{/if}
</div>
<div class="teams">
	<div class="team">
		<TeamComponent data={homeTeam} report={report.home} />
	</div>
	<div class="team">
		<TeamComponent data={awayTeam} report={report.away} />
	</div>
</div>

<style>
	.scoreboard {
		margin: var(--gutter-y) auto;
		width: 600px;
		font-family: var(--font-mono);
	}

	.match-minute {
		display: block;
		text-align: center;
		background: var(--brand-alternate-color);
		padding: 1rem 0;
		cursor: pointer;
	}

	.scores {
		display: flex;
		justify-content: center;
		align-content: center;
		padding: calc(var(--gutter) / 2);
		background: var(--brand-color);
		color: #fff;
		font-size: var(--font-size-headline);
	}

	.scoreboard span {
		padding: 0 1rem;
	}

	.score-sheet {
		display: flex;
		flex-direction: column;
		padding: calc(var(--gutter) / 2);
		background: var(--brand-secondary-color);
	}

	.score-sheet__item {
		display: flex;
	}

	.score-sheet__item.away {
		justify-content: flex-end;
	}

	.score-sheet__item > div {
		display: flex;
		justify-content: space-between;
		width: 50%;
	}

	.score-sheet__item.away > div {
		flex-direction: row-reverse;
	}

	.score-sheet__item__name {
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	.teams {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
	}

	.team {
		display: flex;
		justify-content: center;
	}
</style>
