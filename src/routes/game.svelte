<script lang="ts">
	import { onMount } from 'svelte';

	export let rows = 4;
	onMount(() => {
		let squares: HTMLDivElement[] = [];
		let count = 0;
		let grid = document.getElementById('grid');

		function randomNumber() {
			return Math.floor(Math.random() * (rows * rows));
		}

		function spawnRandomNum() {
			let id = randomNumber();
			if (checkIfEmpty(id)) {
				squares[id].innerHTML = '2';
			} else {
				spawnRandomNum();
			}
			addColors();
		}

		function checkIfEmpty(id: number) {
			let val = squares[id].innerHTML;
			return val == '0';
		}

		function checkGameOver() {
			for (let i = 0; i < squares.length; i++) {
				let el = squares[i].innerHTML;
				if (el == '0') {
					return false;
				}
			}
			return true;
		}

		function initializeGame() {
			while (count < rows * rows) {
				let cell = document.createElement('div');
				cell.className = 'flex w-20 h-20 bg-gray-300 items-center justify-center';
				cell.id = `${count}`;
				cell.innerHTML = `0`;
				squares.push(cell);
				grid?.append(cell);
				count++;
			}
			spawnRandomNum();
			spawnRandomNum();
		}

		function goRight() {
			for (let i = 0; i < squares.length; i++) {
				if (i % rows == 0) {
					let el_1 = squares[i];
					let el_2 = squares[i + 1];
					let el_3 = squares[i + 2];
					let el_4 = squares[i + 3];
					let row = [
						parseInt(el_1.innerHTML),
						parseInt(el_2.innerHTML),
						parseInt(el_3.innerHTML),
						parseInt(el_4.innerHTML)
					];
					let filteredRow = row.filter((e) => e);
					let missing = rows - filteredRow.length;
					let zeroes = Array(missing).fill(0);
					let newRow = zeroes.concat(filteredRow);

					el_1.innerHTML = newRow[0];
					el_2.innerHTML = newRow[1];
					el_3.innerHTML = newRow[2];
					el_4.innerHTML = newRow[3];
				}
			}
		}

		function goLeft() {
			for (let i = 0; i < squares.length; i++) {
				if (i % rows == 0) {
					let el_1 = squares[i];
					let el_2 = squares[i + 1];
					let el_3 = squares[i + 2];
					let el_4 = squares[i + 3];
					let row = [
						parseInt(el_1.innerHTML),
						parseInt(el_2.innerHTML),
						parseInt(el_3.innerHTML),
						parseInt(el_4.innerHTML)
					];
					let filteredRow = row.filter((e) => e);
					let missing = rows - filteredRow.length;
					let zeroes = Array(missing).fill(0);
					let newRow = filteredRow.concat(zeroes);

					el_1.innerHTML = newRow[0].toString();
					el_2.innerHTML = newRow[1].toString();
					el_3.innerHTML = newRow[2].toString();
					el_4.innerHTML = newRow[3].toString();
				}
			}
		}

		function goUp() {
			for (let i = 0; i < rows; i++) {
				let el_1 = squares[i];
				let el_2 = squares[i + rows];
				let el_3 = squares[i + rows * 2];
				let el_4 = squares[i + rows * 3];
				let row = [
					parseInt(el_1.innerHTML),
					parseInt(el_2.innerHTML),
					parseInt(el_3.innerHTML),
					parseInt(el_4.innerHTML)
				];
				let filteredRow = row.filter((e) => e);
				let missing = rows - filteredRow.length;
				let zeroes = Array(missing).fill(0);
				let newRow = filteredRow.concat(zeroes);

				el_1.innerHTML = newRow[0].toString();
				el_2.innerHTML = newRow[1].toString();
				el_3.innerHTML = newRow[2].toString();
				el_4.innerHTML = newRow[3].toString();
			}
		}

		function goDown() {
			for (let i = 0; i < rows; i++) {
				let el_1 = squares[i];
				let el_2 = squares[i + rows];
				let el_3 = squares[i + rows * 2];
				let el_4 = squares[i + rows * 3];
				let row = [
					parseInt(el_1.innerHTML),
					parseInt(el_2.innerHTML),
					parseInt(el_3.innerHTML),
					parseInt(el_4.innerHTML)
				];
				let filteredRow = row.filter((e) => e);
				let missing = rows - filteredRow.length;
				let zeroes = Array(missing).fill(0);
				let newRow = zeroes.concat(filteredRow);

				el_1.innerHTML = newRow[0];
				el_2.innerHTML = newRow[1];
				el_3.innerHTML = newRow[2];
				el_4.innerHTML = newRow[3];
			}
		}

		function combineRow() {
			for (let i = 0; i < squares.length - 1; i++) {
				if (
					squares[i].innerHTML === squares[i + 1].innerHTML &&
					parseInt(squares[i].innerHTML) > 0
				) {
					let combinedTotal = parseInt(squares[i].innerHTML) + parseInt(squares[i + 1].innerHTML);
					squares[i].innerHTML = `${combinedTotal}`;
					squares[i + 1].innerHTML = '0';
				}
			}
		}

		function combineCol() {
			for (let i = 0; i < 12; i++) {
				if (
					squares[i].innerHTML === squares[i + rows].innerHTML &&
					parseInt(squares[i].innerHTML) > 0
				) {
					let combinedTotal =
						parseInt(squares[i].innerHTML) + parseInt(squares[i + rows].innerHTML);
					squares[i].innerHTML = `${combinedTotal}`;
					squares[i + rows].innerHTML = '0';
				}
			}
		}

		function keyRight() {
			goRight();
			combineRow();
			goRight();
			if (checkGameOver()) {
				return;
			}
			spawnRandomNum();
		}

		function keyLeft() {
			goLeft();
			combineRow();
			goLeft();
			if (checkGameOver()) {
				return;
			}
			spawnRandomNum();
		}

		function keyUp() {
			goUp();
			combineCol();
			goUp();
			if (checkGameOver()) {
				return;
			}
			spawnRandomNum();
		}

		function keyDown() {
			goDown();
			combineCol();
			goDown();
			if (checkGameOver()) {
				return;
			}
			spawnRandomNum();
		}

		function addColors() {
			for (let i = 0; i < squares.length; i++) {
				switch (squares[i].innerHTML) {
					case '0':
						squares[i].style.backgroundColor = "#afa192"
						break;
					case '2':
						squares[i].style.backgroundColor = "#afa192"
				}
			}
		}

		let touchstartX = 0;
		let touchendX = 0;
		let touchstartY = 0;
		let touchendY = 0;

		function checkDirection() {
			if (touchendX + 10 < touchstartX) keyLeft();
			if (touchendX > touchstartX + 10) keyRight();
			if (touchendY > touchstartY + 10) keyUp();
			if (touchendY + 10 < touchstartY) keyDown();
		}

		document.addEventListener('touchstart', (e) => {
			touchstartX = e.changedTouches[0].screenX;
			touchstartY = e.changedTouches[0].screenY;
		});

		document.addEventListener('touchend', (e) => {
			touchendY = e.changedTouches[0].screenY;
			touchendX = e.changedTouches[0].screenX;
			checkDirection();
		});

		document.onkeydown = function (event) {
			switch (event.code) {
				case 'ArrowRight':
					keyRight();
					break;
				case 'ArrowLeft':
					keyLeft();
					break;
				case 'ArrowUp':
					keyUp();
					break;
				case 'ArrowDown':
					keyDown();
					break;
			}
		};

		initializeGame();
	});
</script>

<div id="grid" class="grid grid-cols-{rows} font-bold text-4xl gap-2"></div>
