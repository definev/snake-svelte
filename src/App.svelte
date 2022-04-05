<script>
  let direction = 'ArrowDown';
  let snakeCoors = [
    [4, 3],
    [4, 4],
    [4, 5],
    [4, 6],
    [4, 7],
  ];
  let foodCoor = [4, 1];

  $: maps = Array.from({ length: 40 }, (_, y) =>
    Array.from({ length: 40 }, (_, x) => {
      let coor = [x, y];
      if (
        snakeCoors[snakeCoors.length - 1][0] === x &&
        snakeCoors[snakeCoors.length - 1][1] === y
      )
        return 3;
      for (let i = 0; i < snakeCoors.length; i++) {
        let snakeCoor = snakeCoors[i];
        if (snakeCoor[0] === coor[0] && snakeCoor[1] === coor[1]) return 1;
      }
      if (foodCoor[0] === coor[0] && foodCoor[1] === coor[1]) return 2;
      return 0;
    })
  );
  $: flatMaps = maps.flat();

  document.onkeydown = function (e) {
    direction = `${e.code}`;
  };

  function canEat(direction) {
    if (
      direction === 'ArrowUp' &&
      snakeCoors[snakeCoors.length - 1][0] === foodCoor[0] &&
      snakeCoors[snakeCoors.length - 1][1] - 1 == foodCoor[1]
    )
      return true;
    if (
      direction === 'ArrowDown' &&
      snakeCoors[snakeCoors.length - 1][0] === foodCoor[0] &&
      snakeCoors[snakeCoors.length - 1][1] + 1 == foodCoor[1]
    )
      return true;
    if (
      direction === 'ArrowLeft' &&
      snakeCoors[snakeCoors.length - 1][1] === foodCoor[1] &&
      snakeCoors[snakeCoors.length - 1][0] - 1 == foodCoor[0]
    )
      return true;
    if (
      direction === 'ArrowRight' &&
      snakeCoors[snakeCoors.length - 1][1] === foodCoor[1] &&
      snakeCoors[snakeCoors.length - 1][0] + 1 == foodCoor[0]
    )
      return true;
  }

  function onMove(direction) {
    let canEated = canEat(direction);
    if (canEated === true) {
      let preFoodCoor = foodCoor;
      let newFoodCoor = [
        Math.round(Math.random() * 39),
        Math.round(Math.random() * 39),
      ];
      while (snakeCoors.includes(newFoodCoor)) {
        newFoodCoor = [
          Math.round(Math.random() * 39),
          Math.round(Math.random() * 39),
        ];
      }
      foodCoor = newFoodCoor;
      snakeCoors = [...snakeCoors, preFoodCoor];
    } else {
      let lastSnakeCoor = snakeCoors[snakeCoors.length - 1];
      let newSnakeCoor;
      if (direction === 'ArrowUp')
        newSnakeCoor = [lastSnakeCoor[0], lastSnakeCoor[1] - 1];
      if (direction === 'ArrowDown')
        newSnakeCoor = [lastSnakeCoor[0], lastSnakeCoor[1] + 1];
      if (direction === 'ArrowLeft')
        newSnakeCoor = [lastSnakeCoor[0] - 1, lastSnakeCoor[1]];
      if (direction === 'ArrowRight')
        newSnakeCoor = [lastSnakeCoor[0] + 1, lastSnakeCoor[1]];

      snakeCoors = [...snakeCoors.slice(1), newSnakeCoor];
    }
  }

  function goUp() {
    onMove('ArrowUp');
  }
  function goDown() {
    onMove('ArrowDown');
  }
  function goLeft() {
    onMove('ArrowLeft');
  }
  function goRight() {
    onMove('ArrowRight');
  }

  function handleEvent() {
    if (direction === 'ArrowUp') goUp();
    if (direction === 'ArrowDown') goDown();
    if (direction === 'ArrowLeft') goLeft();
    if (direction === 'ArrowRight') goRight();
  }

  function changeDirection(newDirection) {
    direction = newDirection;
  }

  setInterval(handleEvent, 200);
</script>

<div class="maps">
  {#each flatMaps as line}
    <div
      class={line === 0
        ? 'ground'
        : line === 1
        ? 'snake'
        : line === 2
        ? 'food'
        : 'snake-head'}
    />
  {/each}
</div>

<div style="display: flex; margin: 20px;">
  <h1 style="margin-right: 40px;">Score: {snakeCoors.length - 5}</h1>
  <div style="display: flex; flex-direction: column; align-items: center;">
    <button on:click={() => changeDirection('ArrowUp')}>Up</button>
    <div>
      <button
        style="margin-right: 50px;"
        on:click={() => changeDirection('ArrowLeft')}>Left</button
      >

      <button on:click={() => changeDirection('ArrowRight')}>Right</button>
    </div>
    <button on:click={() => changeDirection('ArrowDown')}>Down</button>
  </div>
</div>

<style>
  .maps {
    display: grid;
    grid-template-columns: repeat(40, 10px);
    grid-template-rows: repeat(40, 10px);
    border: 10px solid;
    width: 400px;
    background-color: wheat;
  }

  .ground {
    background-color: wheat;
  }

  .snake {
    background-color: red;
    border-radius: 5px;
  }

  .snake-head {
    background-color: aqua;
    border-radius: 5px;
  }

  .food {
    background-color: yellow;
    border-radius: 5px;
  }

  button {
    height: 80px;
    width: 80px;
    background-color: burlywood;
    border-radius: 50%;
    margin: -10px;
    border: none;
  }
  
  button:hover {
    border: 3px solid;
  }
</style>
