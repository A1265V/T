<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Every Game Ever — Master Library</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Bebas+Neue&display=swap" rel="stylesheet"/>
<style>
  :root {
    --bg: #080808;
    --bg2: #111;
    --bg3: #161616;
    --border: #222;
    --text: #ddd;
    --muted: #555;
    --dim: #333;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { background: var(--bg); color: var(--text); font-family: 'Space Mono', monospace; min-height: 100vh; }

  /* HEADER */
  .header { background: var(--bg2); border-bottom: 1px solid var(--border); padding: 20px 24px 0; position: sticky; top: 0; z-index: 100; }
  .header-top { display: flex; align-items: baseline; gap: 14px; margin-bottom: 6px; }
  .title { font-family: 'Bebas Neue', sans-serif; font-size: 36px; letter-spacing: 3px; color: #fff; line-height: 1; }
  .total-badge { font-size: 10px; background: #fff; color: #000; padding: 3px 8px; font-weight: 700; letter-spacing: 0.1em; }
  .subtitle { font-size: 10px; color: var(--muted); letter-spacing: 0.12em; margin-bottom: 16px; }
  .search-wrap { position: relative; }
  .search { width: 100%; background: var(--bg); border: 1px solid var(--border); border-bottom: none; color: #fff; padding: 10px 14px 10px 36px; font-size: 12px; font-family: inherit; outline: none; }
  .search::placeholder { color: var(--muted); }
  .search-icon { position: absolute; left: 12px; top: 50%; transform: translateY(-50%); color: var(--muted); font-size: 14px; }
  .tabs { display: flex; gap: 0; overflow-x: auto; scrollbar-width: none; margin-top: 14px; }
  .tabs::-webkit-scrollbar { display: none; }
  .tab { background: transparent; border: none; color: var(--muted); padding: 8px 16px; font-size: 10px; font-family: inherit; font-weight: 700; cursor: pointer; white-space: nowrap; letter-spacing: 0.08em; border-top: 3px solid transparent; transition: all 0.15s; }
  .tab.active { color: #fff; border-top-color: var(--accent, #fff); background: var(--bg); }
  .tab:hover:not(.active) { color: #aaa; }

  /* CONTENT */
  .content { padding: 24px; }
  .cat-header { display: flex; align-items: center; gap: 12px; margin-bottom: 20px; }
  .cat-emoji { font-size: 28px; }
  .cat-name { font-family: 'Bebas Neue', sans-serif; font-size: 28px; letter-spacing: 2px; }
  .cat-count { font-size: 10px; color: var(--muted); letter-spacing: 0.1em; }
  .games-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 8px; }
  .game-card { background: var(--bg2); border: 1px solid var(--border); border-left: 3px solid var(--accent, #fff); padding: 12px 14px; cursor: default; transition: background 0.1s; }
  .game-card:hover { background: var(--bg3); }
  .game-num { font-size: 9px; color: var(--dim); margin-bottom: 4px; letter-spacing: 0.1em; }
  .game-name { font-size: 13px; font-weight: 700; color: #fff; margin-bottom: 4px; }
  .game-desc { font-size: 11px; color: var(--muted); line-height: 1.6; }

  /* SEARCH RESULTS */
  .search-results { }
  .search-label { font-size: 10px; color: var(--muted); letter-spacing: 0.12em; margin-bottom: 16px; }
  .search-card { background: var(--bg2); border: 1px solid var(--border); border-left: 3px solid var(--accent2, #fff); padding: 12px 14px; margin-bottom: 8px; }
  .search-cat-tag { font-size: 9px; color: var(--muted); letter-spacing: 0.08em; margin-bottom: 4px; }

  /* FOOTER */
  .footer { margin-top: 40px; padding: 16px; border: 1px solid var(--border); display: flex; justify-content: space-between; font-size: 10px; color: var(--dim); letter-spacing: 0.1em; }

  .hidden { display: none !important; }
</style>
</head>
<body>

<div class="header">
  <div class="header-top">
    <div class="title">EVERY GAME EVER</div>
    <div class="total-badge" id="totalBadge">— GAMES</div>
  </div>
  <div class="subtitle">YOUR MASTER GAME LIBRARY · POPULAR GAMES EVERYONE KNOWS</div>
  <div class="search-wrap">
    <span class="search-icon">⌕</span>
    <input class="search" id="searchInput" placeholder="Search any game..." oninput="handleSearch(this.value)"/>
  </div>
  <div class="tabs" id="tabs"></div>
</div>

<div class="content">
  <div id="categoryView"></div>
  <div id="searchView" class="hidden search-results"></div>
</div>

<script>
const CATEGORIES = [
  {
    id:"board", emoji:"♟️", label:"Board Games", color:"#C8873A",
    games:[
      {n:"Chess",d:"The ultimate strategy game — move pieces across an 8x8 board and checkmate the opponent's king."},
      {n:"Checkers / Draughts",d:"Jump over and capture your opponent's pieces diagonally across a checkered board."},
      {n:"Ludo",d:"Race your four tokens from start to home while sending opponents back to theirs."},
      {n:"Snakes & Ladders",d:"Roll dice, climb ladders, and avoid snakes in this classic game of pure luck."},
      {n:"Monopoly",d:"Buy properties, build houses, collect rent, and bankrupt everyone else on the board."},
      {n:"Scrabble",d:"Form words on a grid using letter tiles — more valuable letters and squares score more points."},
      {n:"Battleship",d:"Guess coordinates to sink your opponent's hidden fleet before they sink yours."},
      {n:"Connect Four",d:"Drop colored discs into a vertical grid and be the first to connect four in a row."},
      {n:"Risk",d:"Conquer territories and continents by rolling dice in this global domination strategy game."},
      {n:"Cluedo / Clue",d:"Deduce the murderer, weapon, and room in this classic mystery board game."},
      {n:"Jenga",d:"Pull wooden blocks from a tower and stack them on top without toppling the whole thing."},
      {n:"Guess Who?",d:"Ask yes/no questions to figure out which character your opponent secretly picked."},
      {n:"Backgammon",d:"Race your 15 pieces around the board and bear them off before your opponent does."},
      {n:"Othello / Reversi",d:"Flip your opponent's discs to your color — the player with the most discs at the end wins."},
      {n:"Chinese Checkers",d:"Race your marbles to the opposite star point of the board by jumping over pieces."},
      {n:"Boggle",d:"Find as many words as possible in a grid of random letters within a time limit."},
      {n:"Trivial Pursuit",d:"Answer trivia questions across six categories to collect all the pie slices and win."},
      {n:"Taboo",d:"Describe a word to your team without using the listed forbidden words."},
      {n:"Pictionary",d:"Draw a word and have your team guess it before time runs out."},
      {n:"Mastermind",d:"Crack the secret color code in as few guesses as possible using logic and deduction."},
      {n:"Stratego",d:"A two-player battle game — hide your flag and capture the enemy's using ranked pieces."},
      {n:"Carrom",d:"Flick a striker to pocket your pieces into corner pockets on a smooth square board."},
      {n:"Mahjong",d:"Match and clear tiles from a layered stack in this classic Chinese tile game."},
      {n:"Go",d:"Place black or white stones to surround and capture more territory than your opponent."},
      {n:"Nine Men's Morris",d:"Place and move pieces to form rows of three and remove the opponent's pieces."},
      {n:"Halma",d:"Move all your pieces to the opposite corner before your opponent does the same."},
      {n:"Parcheesi / Pachisi",d:"The original Indian cross-and-circle game — race pieces around a plus-shaped board."},
      {n:"Trouble",d:"Race your pegs around the board while sending opponents back to start with the Pop-O-Matic bubble."},
      {n:"Sorry!",d:"Draw cards and move pawns around the board — bump others back to start with a 'Sorry!'"},
      {n:"Yahtzee",d:"Roll five dice up to three times and score combinations like full house, straight, or Yahtzee."},
      {n:"Settlers of Catan",d:"Collect resources, build roads and settlements, and trade your way to 10 victory points."},
      {n:"Ticket to Ride",d:"Collect train cards and claim railway routes to connect cities across the map."},
      {n:"Carcassonne",d:"Place tiles to build cities, roads, and farms — then score by placing your followers on them."},
      {n:"Codenames",d:"Give one-word clues to help your team guess secret agent words without hitting the assassin."},
      {n:"Dominion",d:"Build your deck by buying action, treasure, and victory cards to score the most points."},
      {n:"Hive",d:"A tile-based game with no board — surround the opponent's queen bee to win."},
      {n:"Blokus",d:"Fit your colored tetromino pieces onto the board, touching only corners of your own pieces."},
      {n:"Quoridor",d:"Move your pawn to the other side while placing walls to block your opponent."},
      {n:"Rummikub",d:"Arrange numbered tiles into sets and runs to be the first to empty your rack."},
      {n:"Qwirkle",d:"Match tiles by color or shape to build lines and score points — like a colorful Scrabble with shapes."},
      {n:"Sequence",d:"Match cards in your hand to board spaces to form a sequence of five chips."},
      {n:"King of Tokyo",d:"Roll dice to attack, heal, and score points while battling to become the monster king of Tokyo."},
      {n:"Coup",d:"Use bluffing and deduction to eliminate opponents' influences in this hidden-role card game."},
      {n:"Love Letter",d:"A micro card game — use character abilities to get your love letter to the princess."},
      {n:"Sushi Go",d:"Draft sushi cards to make the best meal combos and score the most points over three rounds."},
      {n:"Takenoko",d:"Grow bamboo, move the panda, and complete objective cards in this peaceful Japanese garden game."},
      {n:"Shogi",d:"Japanese chess where captured pieces switch sides and can be dropped back onto the board."},
      {n:"Xiangqi / Chinese Chess",d:"Chinese chess played on intersections of a 9x10 board with a river dividing the two sides."},
      {n:"Mancala",d:"Scoop seeds from pits and distribute them around the board — land in your store to score."},
      {n:"Oware",d:"Classic African mancala variant — sow seeds and capture rows to end up with the most."},
      {n:"Dots and Boxes",d:"Take turns drawing lines — complete a box to claim it; the player with the most boxes wins."},
      {n:"Tic-Tac-Toe",d:"Classic X's and O's — get three in a row on a 3x3 grid before your opponent does."},
      {n:"Ultimate Tic-Tac-Toe",d:"Each move sends your opponent to a corresponding mini-board in this nested strategy variant."},
      {n:"Nim",d:"Take turns removing objects from rows — force your opponent to take the last one and lose."},
      {n:"Peg Solitaire",d:"Jump pegs over each other to remove them — try to leave just one peg in the center."},
      {n:"Fanorona",d:"Traditional Malagasy strategy game involving capturing by approach or withdrawal."},
      {n:"Draughts variants",d:"International, Turkish, Brazilian, Russian — each version of checkers with its own rules and board size."},
    ]
  },
  {
    id:"card", emoji:"🃏", label:"Card Games", color:"#C84040",
    games:[
      {n:"Solitaire — Klondike",d:"The world's most played card game — build four foundation piles from Ace to King."},
      {n:"Solitaire — Spider",d:"Arrange all cards into sequences by suit using one or two decks across 10 columns."},
      {n:"Solitaire — FreeCell",d:"Almost every deal is solvable — move all cards to foundations using four free cells as buffers."},
      {n:"Solitaire — Pyramid",d:"Pair cards that add up to 13 to remove them from a pyramid-shaped layout."},
      {n:"Solitaire — Tri-Peaks",d:"Clear three peaks of cards by building sequences one higher or lower than the top card."},
      {n:"Solitaire — Golf",d:"Remove cards from a tableau onto a waste pile in sequences — finish with the lowest score."},
      {n:"Poker — Texas Hold'em",d:"Use two hole cards and five community cards to make the best five-card hand."},
      {n:"Poker — Omaha",d:"Like Hold'em but you get four hole cards and must use exactly two of them."},
      {n:"Poker — 5-Card Draw",d:"Draw replacement cards to improve your hand — the oldest and simplest poker format."},
      {n:"Blackjack / 21",d:"Get cards as close to 21 as possible without going over to beat the dealer."},
      {n:"Rummy",d:"Draw and discard to form sets and runs — be the first to meld all your cards."},
      {n:"Gin Rummy",d:"Form melds and knock when your unmatched cards total 10 or less to win the round."},
      {n:"Indian Rummy",d:"Popular 13-card rummy variant — form at least two sequences, one of which must be pure."},
      {n:"Hearts",d:"Avoid collecting hearts and the queen of spades — or collect them all for 'shooting the moon.'"},
      {n:"Spades",d:"Bid the number of tricks you'll win, then hit that bid exactly as a team."},
      {n:"Bridge",d:"A complex trick-taking game with bidding — widely considered the greatest card game ever made."},
      {n:"Cribbage",d:"Score points by making combinations of 15, pairs, runs, and flushes — then peg your score on a board."},
      {n:"Go Fish",d:"Ask opponents for cards to build four-of-a-kind sets — if they don't have it, go fish from the deck."},
      {n:"War",d:"Flip cards simultaneously — the higher card wins both. Ties go to war."},
      {n:"Snap",d:"Slap the pile when two identical cards appear in a row to win all the cards."},
      {n:"Old Maid",d:"Draw cards and make pairs — the player left holding the odd queen at the end loses."},
      {n:"Crazy Eights",d:"Play cards matching the suit or rank of the top card — play an eight to change the suit."},
      {n:"UNO",d:"Match colors and numbers, use action cards to disrupt opponents, and shout UNO when you have one left."},
      {n:"Skip-Bo",d:"Use your stockpile and hand to build sequential building piles — first to empty their stockpile wins."},
      {n:"Phase 10",d:"Complete ten specific phases of card combinations in order — players move at their own pace."},
      {n:"Canasta",d:"A rummy-style game — form melds of seven cards called canastas to score big."},
      {n:"Egyptian Rat Screw",d:"Slap the pile on doubles, sandwiches, or other patterns to steal all the cards."},
      {n:"Speed / Spit",d:"Play cards as fast as you can onto central piles — first to empty your hand wins."},
      {n:"Memory / Concentration",d:"Flip pairs of face-down cards and remember their positions to collect the most matched pairs."},
      {n:"Durak",d:"Russia's most popular card game — attack and defend until one player is left holding cards — they're the fool."},
      {n:"Big Two",d:"Play higher combinations than the previous player — last to empty their hand loses."},
      {n:"President / Asshole",d:"Play out your hand in ascending combos — your rank next round is determined by how you finish."},
      {n:"Kemps",d:"Signal your partner secretly when you have four of a kind — and stop the other team from doing the same."},
      {n:"Bullshit / Cheat",d:"Play cards face down calling their rank — if you're caught lying, take the pile. If not, you're safe."},
      {n:"Palace / Shithead",d:"Clear your hand by playing higher cards onto the previous — avoid being the last player with cards."},
      {n:"Skat",d:"Germany's national card game — three players bid to win the most points in trick-taking."},
      {n:"Briscola",d:"Italy's most popular card game — win tricks using Briscola trump cards to score points."},
      {n:"Scopa",d:"Italian fishing card game — capture table cards that add up to 15 to score."},
      {n:"Exploding Kittens",d:"Draw cards and avoid the exploding kitten — use action cards to defuse or redirect disaster."},
      {n:"Dobble / Spot It!",d:"Every two cards share exactly one symbol — shout it first to win the card."},
      {n:"Skull",d:"Place roses or a skull face down, bid on how many you can flip without hitting a skull."},
      {n:"Euchre",d:"A trick-taking game for four where only 24 cards are used — trump is determined each round."},
      {n:"Whist",d:"The ancestor of Bridge — win tricks for your partnership with no bidding required."},
      {n:"Pinochle",d:"Score points by melding card combinations and winning tricks in this classic American game."},
      {n:"Baccarat",d:"Bet on the player or banker hand — whichever is closest to 9 wins."},
    ]
  },
  {
    id:"puzzle", emoji:"🧩", label:"Puzzle & Logic", color:"#4A90D9",
    games:[
      {n:"Sudoku",d:"Fill a 9x9 grid so every row, column, and 3x3 box contains the numbers 1 through 9."},
      {n:"Crossword",d:"Fill a grid with words based on numbered across and down clues."},
      {n:"Word Search",d:"Find hidden words running in any direction within a grid of letters."},
      {n:"Jigsaw Puzzle",d:"Assemble hundreds of interlocking pieces to recreate a complete image."},
      {n:"Nonogram / Picross",d:"Use row and column number clues to reveal a hidden pixel art picture."},
      {n:"Kakuro",d:"Like a crossword but with numbers — fill runs of cells so they add to the given clue sum."},
      {n:"Minesweeper",d:"Uncover all safe squares on a grid without triggering hidden mines using number hints."},
      {n:"2048",d:"Slide tiles to merge matching numbers and reach the 2048 tile."},
      {n:"Threes",d:"Slide and merge numbered tiles in multiples of three — try to reach the highest number."},
      {n:"Sokoban",d:"Push crates onto target spots in a warehouse — plan every move because you can't pull them."},
      {n:"Tangram",d:"Arrange seven flat geometric shapes to form a target silhouette."},
      {n:"Rubik's Cube",d:"Rotate layers of a 3x3 colored cube until each face is a single solid color."},
      {n:"Sliding Puzzle / 15-Puzzle",d:"Slide numbered tiles into order by moving them through a single empty space."},
      {n:"Tower of Hanoi",d:"Move a stack of discs from one peg to another — never place a larger disc on a smaller one."},
      {n:"Lights Out",d:"Toggle lights on a grid so all are off — each press flips adjacent lights too."},
      {n:"Hitori",d:"Shade cells so no number appears twice in any row or column and shaded cells don't touch."},
      {n:"Nurikabe",d:"Fill cells black or white — white islands match numbered clues, black cells form a connected sea."},
      {n:"Slitherlink",d:"Draw a single closed loop through grid dots using number clues for how many sides each cell has."},
      {n:"Fillomino",d:"Divide the grid into polyominoes so each region contains its size number exactly."},
      {n:"Masyu",d:"Draw a loop that passes through all circles — straight through white ones, turning at black ones."},
      {n:"Hashiwokakero / Bridges",d:"Connect numbered islands with bridges that match their count and form one connected group."},
      {n:"KenKen",d:"Fill a grid like Sudoku using arithmetic — each cage must equal its clue using the given operation."},
      {n:"Skyscrapers",d:"Place buildings so the view count from each edge matches the clue number."},
      {n:"Numberlink",d:"Connect matching number pairs with non-crossing paths that fill the entire grid."},
      {n:"Tapa",d:"Shade cells to form a connected wall — number clues indicate runs of shaded cells around them."},
      {n:"Star Battle",d:"Place stars in the grid so each row, column, and region has exactly the right number of stars."},
      {n:"Tents and Trees",d:"Place one tent beside each tree so no tents touch and rows and columns match their counts."},
      {n:"Peg Jump",d:"Jump pegs over each other to eliminate them — try to leave just one peg remaining."},
      {n:"River Crossing Puzzles",d:"Logical ferry problems — move everyone across a river safely following specific constraints."},
      {n:"Cryptogram",d:"Decode a message where each letter has been replaced by another letter or symbol."},
      {n:"Logic Grid Puzzles",d:"Use given clues to match people, places, and things in a grid of possibilities."},
      {n:"Spot the Difference",d:"Find all the subtle differences between two nearly identical images."},
      {n:"Hidden Objects",d:"Find a list of cleverly concealed items hidden within a detailed illustrated scene."},
      {n:"Rush Hour / Traffic Jam",d:"Slide cars and trucks out of the way to get the red car to the exit."},
      {n:"Unblock Me",d:"Slide wooden blocks to clear a path for the highlighted block to exit the board."},
      {n:"Futoshiki",d:"Fill a grid with numbers using inequality signs between cells as your only clue."},
      {n:"Killer Sudoku",d:"Sudoku with cages — cells in each cage must add to a given sum with no repeated digits."},
      {n:"Maze Solving",d:"Navigate through a complex maze from entrance to exit without hitting dead ends."},
      {n:"Matchstick Puzzles",d:"Move or remove a set number of matchsticks to fix an equation or form a new shape."},
      {n:"Water Pouring Puzzles",d:"Transfer water between containers of different sizes to reach an exact target amount."},
    ]
  },
  {
    id:"arcade", emoji:"🕹️", label:"Arcade & Digital Classics", color:"#9B59B6",
    games:[
      {n:"Pac-Man",d:"Eat all the dots in a maze while avoiding ghosts — eat a power pellet to turn the tables."},
      {n:"Space Invaders",d:"Shoot rows of descending aliens before they reach the bottom of the screen."},
      {n:"Asteroids",d:"Rotate and thrust your ship to shoot and avoid tumbling space rocks."},
      {n:"Galaga",d:"Shoot alien formations that swoop down in attack patterns — let them capture your ship then win it back."},
      {n:"Donkey Kong",d:"Climb ladders and dodge barrels thrown by a gorilla to rescue the person at the top."},
      {n:"Frogger",d:"Hop a frog safely across a busy road and a river of logs without getting hit."},
      {n:"Centipede",d:"Shoot a segmented centipede winding down a mushroom field before it reaches the bottom."},
      {n:"Breakout / Arkanoid",d:"Control a paddle to bounce a ball and break all the bricks at the top of the screen."},
      {n:"Pong",d:"The original video game — bounce a ball back and forth with a paddle and outscore your opponent."},
      {n:"Snake",d:"Guide a growing snake to eat food without hitting walls or your own tail."},
      {n:"Tetris",d:"Rotate and drop falling tetrominoes to complete solid horizontal lines and clear the board."},
      {n:"Dr. Mario",d:"Drop colored capsules to line up four matching colors and eliminate viruses."},
      {n:"Puyo Puyo",d:"Drop pairs of colored blobs and chain combos to send garbage to your opponent."},
      {n:"Bubble Bobble",d:"Trap enemies in bubbles then pop them — collect treasure in this two-player platformer."},
      {n:"Dig Dug",d:"Dig tunnels underground to inflate and pop enemies or drop rocks on them."},
      {n:"Bomberman",d:"Place bombs to blast through walls and eliminate opponents in a maze-like arena."},
      {n:"Galaga",d:"Shoot alien formations that swoop down in attack patterns — let them capture your ship then win it back."},
      {n:"Q*bert",d:"Hop on all the cubes in a pyramid to change their color while avoiding enemies."},
      {n:"Defender",d:"Fly over a landscape shooting aliens that are trying to abduct humanoids on the surface."},
      {n:"Joust",d:"Ride a flying ostrich and joust against enemy knights — land higher than them to win."},
      {n:"Missile Command",d:"Intercept waves of incoming missiles with your limited anti-ballistic missiles before cities are destroyed."},
      {n:"Zaxxon",d:"An isometric space shooter — fly through a fortress blasting enemies and dodging obstacles."},
      {n:"BurgerTime",d:"Walk over burger ingredients to drop them on enemies below and assemble the burgers."},
      {n:"Popeye",d:"Collect hearts, musical notes, and letters while avoiding Bluto and Sea Hag."},
      {n:"Tapper",d:"Serve drinks to customers at the bar before they reach you — catch empty mugs on the return."},
      {n:"Mappy",d:"A mouse police officer collecting items in a house full of cats while bouncing on trampolines."},
      {n:"Spy Hunter",d:"Drive an armed sports car down an endless road shooting enemy vehicles."},
      {n:"Elevator Action",d:"Descend a building using elevators, shooting enemies and collecting secret documents."},
      {n:"Kung-Fu Master",d:"A side-scrolling brawler — fight through floors of enemies to rescue someone at the top."},
      {n:"Track & Field",d:"Mash buttons to run, jump, and throw in classic Olympic-style events."},
      {n:"Paperboy",d:"Deliver papers to subscribers, avoid hazards, and smash non-subscriber windows on your route."},
      {n:"Gradius",d:"A horizontal space shooter — blast alien forces and collect power-up capsules along the way."},
      {n:"Contra",d:"Run, jump, and shoot through jungles and bases either solo or in co-op against alien forces."},
      {n:"R-Type",d:"A challenging horizontal space shooter — memorize enemy patterns to survive each stage."},
      {n:"Bubble Shooter",d:"Aim and shoot colored bubbles to match groups of three or more and clear the board."},
      {n:"Pinball",d:"Pull back the plunger and use flippers to keep the ball in play and hit high-scoring targets."},
      {n:"Super Hexagon",d:"Rotate a tiny triangle to avoid collapsing walls in this brutally fast reflex game."},
      {n:"Excitebike",d:"Race motocross bikes on side-scrolling tracks — manage your engine heat or overheat and slow down."},
      {n:"Ice Climber",d:"Climb an icy mountain by smashing through platforms while avoiding birds and yetis."},
      {n:"Balloon Fight",d:"Float with balloons and stomp enemy balloonists before they land and reinflate."},
      {n:"Duck Hunt",d:"Use a light gun to shoot ducks flying across the screen before they escape."},
      {n:"Mario Bros (Classic)",d:"Defeat enemies by hitting platforms from below then kicking them — before they flip back over."},
      {n:"Donkey Kong Jr",d:"Play as the son trying to rescue Donkey Kong — climb vines and drop fruit on enemies."},
    ]
  },
  {
    id:"sports", emoji:"🎯", label:"IRL Sports Digitized", color:"#27AE60",
    games:[
      {n:"8-Ball Pool",d:"Pot your seven solids or stripes then sink the 8-ball to win. Don't pocket it early."},
      {n:"9-Ball Pool",d:"Pot balls in numerical order — whoever pockets the 9-ball legally wins."},
      {n:"Snooker",d:"Score points by potting red and colour balls in turn on a large 6-pocket table."},
      {n:"Darts — 501",d:"Start at 501 and count down to exactly zero — must finish on a double."},
      {n:"Darts — Cricket",d:"Close numbers 15–20 and bullseye by hitting them three times before your opponent."},
      {n:"Table Tennis / Ping Pong",d:"Rally a lightweight ball over a net on a table — first to 11 points with a 2-point lead wins."},
      {n:"Air Hockey",d:"Slide a puck on a cushion of air and score in your opponent's goal using a striker."},
      {n:"Bowling",d:"Roll a ball down a lane to knock down all 10 pins — a strike is all in one shot."},
      {n:"Curling",d:"Slide a stone down ice toward a target — teammates sweep the ice to guide it."},
      {n:"Mini Golf / Putt Putt",d:"Putt a golf ball through creative obstacle-filled holes in as few strokes as possible."},
      {n:"Shuffleboard",d:"Slide pucks down a long table to land in scoring zones — knock opponents out of the way."},
      {n:"Foosball / Table Football",d:"Spin rods with miniature players to kick a ball into the opponent's goal."},
      {n:"Skee-Ball",d:"Roll a ball up a ramp to land in rings of different point values."},
      {n:"Basketball — Free Throw",d:"Shoot from the free-throw line — perfect your arc and power for every basket."},
      {n:"Basketball — Arcade",d:"Score as many baskets as possible in timed rounds from various distances."},
      {n:"Archery",d:"Aim and release arrows at a target — wind and timing affect where they land."},
      {n:"Bowling (Ten-Pin)",d:"Same as bowling but with the full scoring system — strikes, spares, and 300 perfect games."},
      {n:"Soccer / Football",d:"Score goals using penalties, free kicks, or full match simulations."},
      {n:"Tennis",d:"Rally, serve, and smash across a net — win sets and games to take the match."},
      {n:"Badminton",d:"Hit a shuttlecock over a net — it's lighter than a ball and moves very differently."},
      {n:"Cricket",d:"Bat against bowlers, score runs, and bowl out the opposition in this beloved sport."},
      {n:"Golf",d:"Hit a ball from tee to hole in as few shots as possible across 18 varied holes."},
      {n:"Boxing",d:"Jab, cross, hook, and uppercut your opponent while blocking and dodging their punches."},
      {n:"Arm Wrestling",d:"Tap or hold a button at the right moment to overpower your opponent's arm."},
      {n:"Can Shooting",d:"Aim and throw a ball to knock down stacked cans — carnival game accuracy challenge."},
      {n:"Ring Toss",d:"Toss rings from a distance and land them around bottle necks to score."},
      {n:"Whack-a-Mole",d:"Tap moles as fast as they pop up from holes — miss one and lose points."},
      {n:"Fishing",d:"Cast your line, wait for a bite, then reel in fish of different sizes and rarity."},
      {n:"Kabaddi",d:"Tag opponents while holding your breath — one raider vs a whole team defending."},
      {n:"Carrom (Sports)",d:"Flick striker to pot your pieces into corner pockets on a square board."},
      {n:"Gilli-Danda",d:"Strike a small stick with a larger one and score based on how far it flies — an ancient Indian sport."},
      {n:"Lagori / Seven Stones",d:"Knock down a pile of stones with a ball then rebuild it before the opponent tags all your team."},
      {n:"Kite Flying",d:"Control your kite's height and direction and cut opponents' strings in aerial dogfights."},
      {n:"Disc Golf",d:"Throw a frisbee toward basket targets around a course in as few throws as possible."},
      {n:"Pétanque / Boules",d:"Toss metal balls to land as close as possible to a small target ball called the cochonnet."},
      {n:"Curling (Casual)",d:"Slide stones toward the target rings — the stone closest to center wins the end."},
      {n:"Horseshoes",d:"Toss horseshoes at a stake — ring it for a ringer or get closest to score."},
      {n:"Bocce Ball",d:"Roll large balls as close as possible to the small target pallino ball."},
    ]
  },
  {
    id:"mobile", emoji:"📱", label:"Mobile Hits", color:"#E91E8C",
    games:[
      {n:"Angry Birds",d:"Slingshot birds at pig-filled structures — different birds have unique abilities."},
      {n:"Candy Crush / Match-3",d:"Swap adjacent candies to match three or more in a row and clear levels with special goals."},
      {n:"Fruit Ninja",d:"Slice flying fruit with a blade — avoid bombs or your game ends."},
      {n:"Temple Run",d:"Run, turn, slide, and jump endlessly through temple ruins while a demon chases you."},
      {n:"Subway Surfers",d:"Dash along train tracks dodging trains, barriers, and the inspector by swiping in any direction."},
      {n:"Jetpack Joyride",d:"Fly with a machine gun jetpack through a lab collecting coins and avoiding obstacles."},
      {n:"Flappy Bird",d:"Tap to keep a bird airborne and fly through gaps between pipes — brutally hard."},
      {n:"Crossy Road",d:"Hop across infinite roads, rivers, and train tracks without getting hit."},
      {n:"Cut the Rope",d:"Cut ropes in the right order to swing candy into a hungry little monster's mouth."},
      {n:"Plants vs Zombies",d:"Place sun-costing plants strategically to stop waves of zombies from reaching your house."},
      {n:"Doodle Jump",d:"Tilt to guide a Doodler upward on platforms — fall through the bottom and it's over."},
      {n:"Alto's Adventure",d:"Snowboard down endless mountainsides grinding rooftops and doing backflips."},
      {n:"Monument Valley",d:"Guide a princess through impossible Escher-like architecture by rotating and sliding structures."},
      {n:"Water Sort Puzzle",d:"Pour colored water between tubes to separate all colors into single-colored tubes."},
      {n:"Screw / Nuts & Bolts Puzzle",d:"Unscrew bolts in the right order to free and organize colored nuts on a crowded board."},
      {n:"Ball Sort",d:"Sort colored balls into tubes so each tube holds only one color."},
      {n:"Stack",d:"Tap to slice and stack platforms — the part that doesn't overlap falls off each time."},
      {n:"Helix Jump",d:"Drop a ball through a helix tower — avoid matching colored platforms or it shatters."},
      {n:"Knife Hit",d:"Throw knives at a rotating log — hit all targets without striking another knife."},
      {n:"Color Switch",d:"Tap a ball through spinning obstacles — it can only pass through segments matching its color."},
      {n:"Hole.io",d:"Control a black hole moving around a city — swallow objects smaller than you to grow."},
      {n:"Paper.io",d:"Expand your colored territory by looping back to your area — cut opponents' tails to eliminate them."},
      {n:"Agar.io",d:"Grow your cell by eating pellets and smaller cells — split and throw yourself to catch prey."},
      {n:"Slither.io",d:"Grow your snake by eating glowing orbs — make opponents crash into you to consume their mass."},
      {n:"1010!",d:"Drop sets of Tetris-like pieces onto a 10x10 grid to complete and clear full rows and columns."},
      {n:"Drop7",d:"Drop numbered discs into a 7x7 grid — a disc clears when its number matches its group size."},
      {n:"Two Dots",d:"Connect dots of the same color in lines or squares to clear them and complete level goals."},
      {n:"Wordle",d:"Guess a five-letter word in six tries — colored tiles show which letters are right or wrong."},
      {n:"Geometry Dash",d:"Jump, fly, and flip through dangerous passages in time with upbeat electronic music."},
      {n:"Crowd City",d:"Lead a crowd through a city absorbing lone pedestrians to grow — clash with other crowds to win."},
      {n:"Count Masters",d:"Multiply your crowd by running through gates then battle enemy crowds at the arena."},
      {n:"Bridge Race",d:"Collect bricks and build bridges to be the first to reach the finish line."},
      {n:"Roller Splat",d:"Roll a ball through a maze painting the floor with color until every tile is covered."},
      {n:"Pull the Pin",d:"Pull pins in the correct order to let colored balls flow into matching colored cups."},
      {n:"Going Balls",d:"Roll a ball through an obstacle course on a thin track without falling off the edges."},
      {n:"Smash Hit",d:"Throw metal balls to break glass obstacles as you glide through a surreal environment."},
      {n:"Alto's Odyssey",d:"Sandboard through a desert, float on hot air balloons, and wall-ride through canyon temples."},
      {n:"Archero",d:"Move to stop and auto-shoot — navigate dungeon rooms with unique weapon upgrades."},
    ]
  },
  {
    id:"word", emoji:"📝", label:"Word Games", color:"#E67E22",
    games:[
      {n:"Wordle",d:"Guess a 5-letter word in 6 tries — yellow means right letter wrong spot, green means perfect."},
      {n:"Hangman",d:"Guess letters one at a time to figure out the hidden word before the stick figure is complete."},
      {n:"Boggle",d:"Find words in a grid of randomly placed letters — longer words score more."},
      {n:"Scrabble-style",d:"Place letter tiles on a board to form words and score points using premium squares."},
      {n:"Word Connect",d:"Swipe between letters to form words and fill in the answer grid."},
      {n:"Word Cookies",d:"Swipe across letters arranged in a circle to bake hidden words."},
      {n:"Crossword",d:"Fill a grid with words that interlock — clues are given for across and down entries."},
      {n:"Word Search",d:"Find words hidden in a grid running across, down, diagonal, or backward."},
      {n:"Anagram",d:"Rearrange scrambled letters to form a real word as quickly as possible."},
      {n:"Word Ladder",d:"Transform one word into another by changing one letter at a time — each step must be a valid word."},
      {n:"Bananagrams",d:"Race to build your own crossword grid using all your tiles — grab new ones to expand it."},
      {n:"TypeRacer",d:"Race against others by typing a text passage as fast and accurately as possible."},
      {n:"SpellTower",d:"Tap letters to spell words in a rising tower of tiles before they reach the top."},
      {n:"Hangman variants",d:"Themed hangman puzzles with categories like movies, animals, cities, and celebrities."},
      {n:"Ghost — Word Game",d:"Take turns adding letters to a word fragment — don't complete a word or lose a life."},
      {n:"20 Questions",d:"Think of something and let the AI ask yes/no questions to guess it within 20 questions."},
      {n:"Word Association",d:"Say a word that's connected to the previous one — hesitate or repeat and you're out."},
      {n:"Shiritori",d:"Say a word that starts with the last letter of the previous word — you can't repeat words."},
      {n:"Compound Word Chain",d:"Build a chain where the second half of each word becomes the first half of the next."},
      {n:"Palindrome Finder",d:"Spot words or phrases that read the same forwards and backward."},
      {n:"Vocabulary Builder",d:"Learn and quiz yourself on difficult words — swipe to mark as known or still learning."},
      {n:"Quordle",d:"Solve four Wordle puzzles simultaneously sharing the same guesses."},
      {n:"Waffle (Word Game)",d:"Swap letters on a waffle-shaped grid to form six interlocking words in as few swaps as possible."},
      {n:"Dordle",d:"Solve two Wordle puzzles at once with seven guesses shared between both."},
      {n:"First & Last Letter",d:"Name a word from a category that starts and ends with the given letters — against the clock."},
      {n:"Category Naming",d:"Name as many items from a given category as possible before time runs out."},
      {n:"Mad Libs style",d:"Fill in blanks with random words then reveal the hilariously nonsensical story."},
      {n:"Jumble Puzzle",d:"Unscramble several words then use circled letters to solve the final punchline puzzle."},
      {n:"Cryptogram",d:"Decode a quote where every letter has been substituted with a different letter."},
      {n:"Spelling Bee",d:"Form words using seven letters where the center letter must always be included."},
    ]
  },
  {
    id:"trivia", emoji:"🧠", label:"Trivia & Quiz", color:"#1ABC9C",
    games:[
      {n:"General Knowledge Trivia",d:"Answer questions spanning history, science, culture, sports, and more."},
      {n:"Sports Trivia",d:"Test your knowledge of records, players, teams, and major moments across all sports."},
      {n:"Movie Trivia",d:"Name actors, directors, quotes, and plot details from films across every era and genre."},
      {n:"Music Trivia",d:"Identify artists, album names, lyrics, and chart hits across all decades."},
      {n:"Science Trivia",d:"Answer questions on biology, chemistry, physics, space, and the natural world."},
      {n:"History Trivia",d:"Test knowledge of world events, empires, wars, dates, and historical figures."},
      {n:"Geography Trivia",d:"Name capitals, countries, rivers, mountains, and landmarks around the world."},
      {n:"Pop Culture Trivia",d:"Questions on memes, social media trends, viral moments, and celebrity news."},
      {n:"Who Wants to Be a Millionaire style",d:"Answer 15 increasingly hard questions with lifelines — get them all right for the jackpot."},
      {n:"Jeopardy style",d:"You're given the answer — buzz in and phrase your response as a question."},
      {n:"Are You Smarter Than a 5th Grader style",d:"Answer elementary school questions — they're harder than they sound."},
      {n:"Family Feud style",d:"Guess the most popular survey answers to beat the opposing team."},
      {n:"True or False",d:"Decide if each statement is fact or fiction — rapid-fire rounds with no multiple choice."},
      {n:"Multiple Choice Blitz",d:"Answer as many multiple choice questions as possible before the timer runs out."},
      {n:"Logo Quiz",d:"Identify major brands from their logos with portions hidden or colors removed."},
      {n:"Flag Quiz",d:"Name the country from its national flag — tested on all 195 flags."},
      {n:"Capital Cities Quiz",d:"Match countries to their capital cities — from obvious to obscure."},
      {n:"Landmark Quiz",d:"Identify famous buildings and natural landmarks from photos."},
      {n:"Movie Quotes Quiz",d:"Identify the film from one of its famous lines."},
      {n:"Fill the Lyrics",d:"Complete the missing word in song lyrics from popular hits across different genres."},
      {n:"Name That Tune",d:"Identify a song from just a few notes or a hummed melody."},
      {n:"Emoji Quiz",d:"Decipher movie titles, phrases, or words represented entirely by emoji."},
      {n:"Science Element Quiz",d:"Identify elements by symbol, atomic number, or properties from the periodic table."},
      {n:"Animal Kingdom Quiz",d:"Classify and identify animals by category, habitat, diet, and appearance."},
      {n:"Date Trivia",d:"Guess the year a famous event, invention, or moment occurred."},
      {n:"TV Show Quiz",d:"Answer questions about popular shows — characters, plots, actors, and iconic moments."},
      {n:"Cartoon Trivia",d:"Questions on animated shows from every decade — characters, catchphrases, and storylines."},
      {n:"Superhero Trivia",d:"Test your Marvel and DC knowledge — powers, origins, alter egos, and storylines."},
      {n:"Video Game Trivia",d:"Identify games from screenshots, characters, music, and release year."},
    ]
  },
  {
    id:"brain", emoji:"⚡", label:"Brain & Memory", color:"#E74C3C",
    games:[
      {n:"Simon Says",d:"A sequence of colored lights and sounds plays — repeat it back in the exact same order."},
      {n:"Memory Matching",d:"Flip face-down cards two at a time and match all identical pairs using only your memory."},
      {n:"Pattern Recognition",d:"Study a visual pattern then identify which option continues or completes it."},
      {n:"Reaction Time Test",d:"Tap as fast as possible the moment a signal appears — measures your raw reflex speed."},
      {n:"Math Blitz",d:"Solve arithmetic equations as fast as possible before the timer runs out."},
      {n:"Stroop Effect",d:"Name the ink color a word is printed in — not the word itself, even when they conflict."},
      {n:"Color Matching",d:"Match colors precisely by mixing sliders or selecting the closest shade shown."},
      {n:"Sequence Memory",d:"A growing sequence of highlighted tiles plays — repeat the full sequence each round."},
      {n:"Dual N-Back",d:"Track positions and sounds simultaneously and remember what appeared N turns ago."},
      {n:"Mental Rotation",d:"Determine if two 3D shapes are identical or mirror images of each other."},
      {n:"Number Memory",d:"Memorize increasingly long strings of digits and type them back correctly."},
      {n:"Word Memory",d:"Read a list of words then recall as many as possible after it disappears."},
      {n:"Face Memory",d:"Study faces and names then pick the right face-name pair from distractors."},
      {n:"Visual Search",d:"Find a target symbol hidden among many similar distractors as fast as possible."},
      {n:"Change Blindness",d:"Spot subtle changes between two alternating images — harder than it sounds."},
      {n:"Speed Arithmetic",d:"Race through additions, subtractions, multiplications, and divisions at increasing speeds."},
      {n:"Number Sequences",d:"Find the pattern in a series of numbers and predict the next value."},
      {n:"Odd One Out",d:"Identify the item in a set that doesn't belong based on a hidden logical rule."},
      {n:"Left Brain Right Brain",d:"Coordinate your two hands doing different tasks simultaneously — a true multitasking test."},
      {n:"Multitasking Challenge",d:"Complete two or three simple tasks simultaneously — performance drops are part of the test."},
      {n:"Working Memory Test",d:"Hold and manipulate information in mind while performing another task at the same time."},
      {n:"Attention Span Test",d:"Sustain focus on a boring target and respond only when the specific trigger appears."},
      {n:"Estimation Game",d:"Guess quantities, distances, or sizes — scored on how close you get rather than exact answers."},
      {n:"Mirror Drawing",d:"Trace a shape while only seeing your hand in a mirror — frustratingly tests coordination."},
    ]
  },
  {
    id:"dice", emoji:"🎲", label:"Dice Games", color:"#8E44AD",
    games:[
      {n:"Yahtzee",d:"Roll five dice up to three times and score combinations — aim for a Yahtzee (five of a kind)."},
      {n:"Farkle",d:"Roll six dice and score sets — but if you roll nothing valid, you Farkle and lose your points."},
      {n:"Bunco",d:"A fast dice party game — roll three dice trying to match the round number and score in teams."},
      {n:"Liar's Dice / Perudo",d:"Make bids on what dice are showing across all players' cups — call bluffs to win."},
      {n:"Shut the Box",d:"Roll dice and flip down numbered tiles that match — try to shut all tiles before you can't move."},
      {n:"Pig",d:"Roll a die and accumulate points — but roll a 1 and lose everything banked that turn."},
      {n:"Craps",d:"Bet on the outcomes of dice rolls — the most exciting casino table game."},
      {n:"Mexico",d:"Roll two dice and read them as a two-digit number — the highest number avoids elimination."},
      {n:"Ship Captain Crew",d:"Roll to get a 6 (ship), 5 (captain), and 4 (crew) in order before scoring your cargo dice."},
      {n:"Beetle",d:"Roll a die to draw body parts of a beetle — first to complete their insect wins."},
      {n:"Crown and Anchor",d:"Bet on symbols then roll three dice — win if your symbol appears."},
      {n:"10,000 / Zilch",d:"Roll six dice and keep scoring combos — reach 10,000 points before other players do."},
      {n:"Qwixx",d:"Roll colored dice and cross off numbers in colored rows — a deceptively deep and fast dice game."},
      {n:"Drop Dead",d:"Roll five dice and score everything except 2s and 5s — those dice are dropped. Keep rolling."},
      {n:"Sevens",d:"Roll two dice — try to score exactly seven combinations to win the round."},
    ]
  },
  {
    id:"casino", emoji:"🎰", label:"Casino Style (No Real Money)", color:"#D4AC0D",
    games:[
      {n:"Slots — Classic 3-Reel",d:"Spin three reels and match symbols across the payline — the simplest slot machine format."},
      {n:"Slots — 5-Reel Video",d:"Modern slots with five reels, multiple paylines, bonus rounds, and animated themes."},
      {n:"Roulette — European",d:"Place bets on numbers, colors, or sections then spin the wheel and drop the ball."},
      {n:"Roulette — American",d:"Same as European but with an extra 00 pocket — slightly lower odds for the player."},
      {n:"Blackjack",d:"Get to 21 or closer than the dealer without going bust — hit, stand, double, or split."},
      {n:"Baccarat",d:"Bet on player, banker, or tie — whichever hand totals closest to 9 wins."},
      {n:"Craps",d:"Bet on the outcomes of dice rolls at a virtual craps table with all the classic betting options."},
      {n:"Keno",d:"Pick numbers from 1–80 and hope the lottery-style draw matches as many as possible."},
      {n:"Bingo",d:"Mark off called numbers on your card — first to complete a line or full card shouts Bingo."},
      {n:"Video Poker",d:"Draw replacement cards to form the best poker hand — payouts depend on hand strength."},
      {n:"Sic Bo",d:"Bet on the outcome of three rolled dice — a classic Asian dice gambling game."},
      {n:"Wheel of Fortune style",d:"Spin a giant prize wheel and win what the pointer lands on — simple and satisfying."},
      {n:"Pachinko style",d:"Drop balls through a vertical pinball-like machine and watch them bounce into prize slots."},
      {n:"Mahjong Solitaire",d:"Match and remove pairs of free tiles from a stacked layout until the board is cleared."},
      {n:"Scratch Cards",d:"Scratch virtual silver panels to reveal symbols — match three to win virtual prizes."},
    ]
  },
  {
    id:"retro", emoji:"👾", label:"Retro & Nostalgic", color:"#00BCD4",
    games:[
      {n:"Oregon Trail",d:"Guide settlers westward — manage supplies, hunt, ford rivers, and hope no one gets dysentery."},
      {n:"Lemmings",d:"Save a parade of oblivious creatures by assigning them roles like digger, blocker, or builder."},
      {n:"Worms",d:"Turn-based artillery — launch bazookas, grenades, and Holy Hand Grenades across destructible terrain."},
      {n:"Pitfall!",d:"Swing on vines, avoid alligators, and jump pits in this Atari jungle adventure."},
      {n:"River Raid",d:"Fly a jet down an endless river shooting enemies and refueling before you run dry."},
      {n:"Enduro",d:"Drive in a racing game across day and night and weather — pass cars to survive each day."},
      {n:"Kaboom!",d:"Catch bombs dropped by a villain using moveable buckets — the pace gets relentless fast."},
      {n:"Adventure (Atari)",d:"Explore a kingdom to find a chalice hidden by a dragon — one of the first graphical adventure games."},
      {n:"Tamagotchi style",d:"Raise and care for a virtual pet — feed it, play with it, and keep it happy or it fades away."},
      {n:"Game & Watch style",d:"Simple LCD handheld games from Nintendo's classic pre-Gameboy era."},
      {n:"Number Munchers",d:"Move your Muncher around a grid eating numbers that match the given math rule."},
      {n:"Math Blaster style",d:"Answer math problems to power your spaceship and battle enemies in educational arcade action."},
      {n:"Carmen Sandiego style",d:"Solve geography clues to chase a globetrotting thief across cities around the world."},
      {n:"Zork / Text Adventure",d:"Type commands to navigate a text-described world — an ancient form of interactive fiction."},
      {n:"Lunar Lander",d:"Carefully burn fuel to descend a spacecraft and touch down softly on the moon's surface."},
      {n:"Mavis Beacon Typing",d:"Improve typing speed and accuracy through structured lessons and timed tests."},
      {n:"Circus Atari",d:"Bounce a clown off a seesaw to pop balloons at the top — an Atari classic."},
    ]
  },
  {
    id:"traditional", emoji:"🌍", label:"Traditional & Cultural", color:"#FF7043",
    games:[
      {n:"Mancala — Kalah",d:"Distribute seeds around the board — land in your store to score and keep the turn."},
      {n:"Mancala — Oware",d:"West African mancala — capture seeds by landing in opponent pits with exactly 2 or 3."},
      {n:"Mancala — Bao",d:"The most complex mancala variant — played in East Africa with deep strategic layering."},
      {n:"Carrom",d:"Flick a striker disc to pot your lighter pieces into corner pockets on a lacquered board."},
      {n:"Shogi",d:"Japanese chess — pieces you capture can be dropped back onto the board on your turn."},
      {n:"Xiangqi / Chinese Chess",d:"Played on a 9x10 board with a river — pieces have unique movement rules and a cannon piece."},
      {n:"Janggi / Korean Chess",d:"Korean chess with circular pieces — generals can face each other for a check."},
      {n:"Makruk / Thai Chess",d:"Southeast Asian chess — a slower game with many endgame nuances."},
      {n:"Chaturanga",d:"The ancient Indian ancestor of chess — the original four-piece army game."},
      {n:"Senet",d:"One of the oldest board games — ancient Egyptians threw casting sticks like dice to move pawns."},
      {n:"Royal Game of Ur",d:"A 4,500-year-old race game from Mesopotamia — roll pyramidal dice to move pawns."},
      {n:"Hnefatafl",d:"Viking strategy game — one player's king escapes while the other player tries to capture it."},
      {n:"Fanorona",d:"Traditional Malagasy game — capture by approaching or withdrawing from a chain of pieces."},
      {n:"Bagh-Chal / Tiger & Goat",d:"Nepali game — tigers hunt goats while goats try to trap all four tigers."},
      {n:"Yut Nori",d:"Korean New Year game — throw four sticks like dice and race horses around a circular track."},
      {n:"Hanafuda",d:"Japanese flower cards — match suits across the months to score combinations."},
      {n:"Gilli-Danda",d:"Strike a small stick with a larger stick — score based on how far the small stick flies."},
      {n:"Pittu Garam / Seven Stones",d:"Throw a ball to topple a stack of stones then rebuild it before the other team tags you all."},
      {n:"Toguz Kumalak",d:"Central Asian mancala played on 9x2 pits — strategy-heavy with tuzduk scoring pits."},
      {n:"Surakarta",d:"Indonesian board game — pieces move on circuits and capture by sliding along the loops."},
      {n:"Mu Torere",d:"Maori board game played on an eight-point star — block your opponent from moving."},
      {n:"Konane",d:"Hawaiian game similar to checkers — capture by jumping and try to leave your opponent no moves."},
    ]
  },
  {
    id:"party", emoji:"🎉", label:"Party & Social", color:"#FF4081",
    games:[
      {n:"Would You Rather",d:"Choose between two equally appealing or terrible options — compare choices with other players."},
      {n:"Never Have I Ever",d:"Reveal experiences by saying what you've never done — players who have done it react."},
      {n:"Two Truths One Lie",d:"State two true things and one lie about yourself — other players vote on which is the lie."},
      {n:"Werewolf / Mafia digital",d:"Hidden-role game — townspeople try to find and eliminate hidden werewolves before being eaten."},
      {n:"Spyfall style",d:"Everyone knows the location except the spy — ask questions to expose them before they guess the place."},
      {n:"Wavelength style",d:"Give a clue that places a concept on a spectrum between two opposites — teammates guess where."},
      {n:"Gartic Phone style",d:"Alternate between drawing and describing in a chain — see how wildly the original prompt mutates."},
      {n:"Quiplash style",d:"Write funny answers to prompts then vote for the best one — no right answers, just laughs."},
      {n:"Fibbage style",d:"Write fake answers to trivia questions to fool other players who can't tell what's real."},
      {n:"Drawful style",d:"Draw a strange prompt on your phone — other players try to guess what it's meant to be."},
      {n:"Charades digital",d:"Act out a word or phrase without speaking while your team guesses in time."},
      {n:"Heads Up style",d:"Hold a card on your head and guess from clues given by others — tilt to pass or guess."},
      {n:"Pictionary digital",d:"Draw a word in under 80 seconds while your team races to guess it correctly."},
      {n:"Quick Draw",d:"Draw a given prompt as fast as you can and let an AI try to guess what it is."},
      {n:"Scattergories style",d:"Name things in categories starting with the rolled letter — unique answers score points."},
      {n:"Bingo — Social",d:"Call numbers and mark your card — play against others for the first BINGO."},
      {n:"Skull",d:"Bet on how many coasters you can flip without hitting your own skull — pure bluffing."},
      {n:"Codenames style",d:"Give one-word clues to link multiple words your team must guess without touching the assassin."},
      {n:"Hot Takes",d:"Share controversial opinions on topics and see who agrees or disagrees the most."},
      {n:"Emoji Storytelling",d:"Build a story using only emoji — other players interpret your visual narrative."},
    ]
  },
  {
    id:"twoplayer", emoji:"👥", label:"Two Player Duels", color:"#FF9800",
    games:[
      {n:"Tic-Tac-Toe",d:"X's vs O's on a 3x3 grid — get three in a row to win. Simple but perfectly balanced."},
      {n:"Connect Four",d:"Drop discs into a 7x6 grid and be the first to connect four vertically, horizontally, or diagonally."},
      {n:"Battleship (1v1)",d:"Place your fleet secretly then take turns guessing grid coordinates to sink each other's ships."},
      {n:"Chess (1v1)",d:"The ultimate head-to-head strategy duel — capture the opponent's king to win."},
      {n:"Checkers (1v1)",d:"Jump over your opponent's pieces to capture them — king your piece to move backward too."},
      {n:"Othello / Reversi (1v1)",d:"Flip the opponent's discs to your color by sandwiching them — most discs at end wins."},
      {n:"Backgammon (1v1)",d:"Race your checkers home and bear them off faster than your opponent — dice add tension."},
      {n:"Go (1v1)",d:"Place stones to surround territory — the most territory at the end wins."},
      {n:"Pong (1v1)",d:"Classic arcade paddle battle — bounce the ball past your opponent's paddle to score."},
      {n:"Air Hockey (1v1)",d:"Slide your mallet on a frictionless table to knock the puck into the opponent's goal."},
      {n:"Table Tennis (1v1)",d:"Serve and rally a tiny ball over a net — first to 11 with a 2-point margin wins."},
      {n:"Darts (1v1)",d:"Take turns throwing at the board — race to zero from 501 or close cricket numbers first."},
      {n:"8-Ball Pool (1v1)",d:"Pocket your seven balls then the 8-ball before your opponent does the same."},
      {n:"Rock Paper Scissors",d:"Choose rock, paper, or scissors simultaneously — each beats one other in a cycle."},
      {n:"Hex (1v1)",d:"Connect your two opposite sides of the board with a chain of your colored pieces."},
      {n:"Nim (1v1)",d:"Take turns removing objects — force your opponent to take the last one."},
      {n:"Dots and Boxes (1v1)",d:"Draw lines between dots — complete a box to claim it. Chains of boxes decide the winner."},
      {n:"Reflex Battle",d:"Both players tap a button the moment a signal appears — fastest reflex wins the round."},
      {n:"Typing Race (1v1)",d:"Type a passage faster and more accurately than your opponent to win the race."},
      {n:"Math Battle (1v1)",d:"Both players see the same equation — buzz in first with the correct answer to score."},
      {n:"Trivia Duel",d:"Answer alternating trivia questions — buzz in first on the other's turn to steal."},
      {n:"Word Battle",d:"Both players are given the same letters — first to form the longest valid word wins."},
      {n:"Carrom (1v1)",d:"Flick and pot your pieces before your opponent — the queen gives bonus points."},
      {n:"Foosball (1v1)",d:"Spin the rods to control players and score goals in this table-top football classic."},
    ]
  },
  {
    id:"hypercasual", emoji:"🌀", label:"Hyper Casual", color:"#76FF03",
    games:[
      {n:"Stack",d:"Tap to drop platforms onto the stack — only the overlapping part stays, rest falls away."},
      {n:"Helix Jump",d:"Drop a bouncing ball down a helix tower — time gaps to avoid colored platforms."},
      {n:"Roller Splat",d:"Roll a ball through a maze, painting every tile as you go — cover the whole floor."},
      {n:"Bridge Race",d:"Collect bricks of your color and build a staircase bridge — first to the finish wins."},
      {n:"Crowd City",d:"Absorb pedestrians to grow your crowd — collide with smaller crowds to steal members."},
      {n:"Count Masters",d:"Run through multiplier gates to grow your crowd then defeat enemy waves."},
      {n:"Pull the Pin",d:"Pull numbered pins in the right order so colored balls fall into matching cups."},
      {n:"Knife Hit",d:"Throw knives into a spinning log without hitting a previously thrown knife."},
      {n:"Color Switch",d:"Navigate a ball through rotating obstacles — only pass through matching colored sections."},
      {n:"Water Sort Puzzle",d:"Pour liquids between tubes to sort all colors into single-color tubes."},
      {n:"Screw Puzzle / Nuts & Bolts",d:"Unscrew bolts from a cluttered board to sort colored nuts into matching groups."},
      {n:"Ball Sort",d:"Sort colored balls into tubes — tubes can only hold balls of one color."},
      {n:"Going Balls",d:"Roll a ball through a narrow obstacle-filled track — fall off and you restart the level."},
      {n:"Wobble Man",d:"Guide a jelly-like character across platforms — its wobbly physics make it hilarious."},
      {n:"Tall Man Run",d:"Run through gates to gain or lose height — be tall enough to smash the final barrier."},
      {n:"Crowd Evolution",d:"Upgrade your crowd by picking the right doors — grow more powerful for the final fight."},
      {n:"Giant Rush",d:"Collect size power-ups to grow massive then crush everything in your final run."},
      {n:"Hair Challenge",d:"Grow your character's hair through gates to build it up then use it to swing past obstacles."},
      {n:"Aquapark",d:"Slide down a waterslide, knock others off the track, and be first to reach the pool."},
      {n:"Stair Race",d:"Collect steps and throw them to build a staircase — reach the top of the building first."},
      {n:"Dunk Hit",d:"Tap to aim a basketball and time the release to sink it through a moving hoop."},
      {n:"Sand Drawing",d:"Draw in colored sand satisfying patterns that cascade and pile naturally."},
      {n:"Soap Cutting",d:"Slice satisfying virtual bars of soap into pieces — ASMR-style casual relaxation."},
      {n:"Pressure Wash",d:"Clean dirty surfaces with a virtual pressure washer — oddly satisfying completion loop."},
      {n:"Color Road",d:"Roll a colored ball and only pass through matching color sections of the road ahead."},
    ]
  }
];

// Build the page
const totalGames = CATEGORIES.reduce((s, c) => s + c.games.length, 0);
document.getElementById("totalBadge").textContent = totalGames + " GAMES";

// Build tabs
const tabs = document.getElementById("tabs");
CATEGORIES.forEach(cat => {
  const btn = document.createElement("button");
  btn.className = "tab";
  btn.textContent = cat.emoji + " " + cat.label.toUpperCase();
  btn.dataset.id = cat.id;
  btn.style.setProperty("--accent", cat.color);
  btn.onclick = () => showCategory(cat.id);
  tabs.appendChild(btn);
});

function showCategory(id) {
  const cat = CATEGORIES.find(c => c.id === id);
  document.querySelectorAll(".tab").forEach(t => {
    t.classList.toggle("active", t.dataset.id === id);
    t.style.setProperty("--accent", CATEGORIES.find(c=>c.id===t.dataset.id).color);
  });
  document.getElementById("searchView").classList.add("hidden");
  const view = document.getElementById("categoryView");
  view.innerHTML = `
    <div class="cat-header">
      <div class="cat-emoji">${cat.emoji}</div>
      <div>
        <div class="cat-name" style="color:${cat.color}">${cat.label}</div>
        <div class="cat-count">${cat.games.length} GAMES IN THIS CATEGORY</div>
      </div>
    </div>
    <div class="games-grid">
      ${cat.games.map((g,i) => `
        <div class="game-card" style="--accent:${cat.color}">
          <div class="game-num">${String(i+1).padStart(2,"0")}</div>
          <div class="game-name">${g.n}</div>
          <div class="game-desc">${g.d}</div>
        </div>
      `).join("")}
    </div>
    <div class="footer">
      <span>MASTER GAME LIBRARY</span>
      <span style="color:${cat.color}">${totalGames} TOTAL GAMES</span>
    </div>
  `;
}

function handleSearch(val) {
  if(val.trim().length < 2) {
    document.getElementById("searchView").classList.add("hidden");
    document.getElementById("categoryView").classList.remove("hidden");
    return;
  }
  document.getElementById("categoryView").classList.add("hidden");
  const sv = document.getElementById("searchView");
  sv.classList.remove("hidden");
  const results = [];
  CATEGORIES.forEach(cat => {
    cat.games.forEach(g => {
      if(g.n.toLowerCase().includes(val.toLowerCase()) || g.d.toLowerCase().includes(val.toLowerCase())) {
        results.push({...g, catLabel: cat.label, catEmoji: cat.emoji, color: cat.color});
      }
    });
  });
  sv.innerHTML = `
    <div class="search-label">${results.length} RESULT${results.length!==1?"S":""} FOR "${val.toUpperCase()}"</div>
    ${results.map(r => `
      <div class="search-card" style="--accent2:${r.color}">
        <div class="search-cat-tag">${r.catEmoji} ${r.catLabel.toUpperCase()}</div>
        <div class="game-name">${r.n}</div>
        <div class="game-desc">${r.d}</div>
      </div>
    `).join("")}
    ${results.length===0 ? '<div style="color:#555;font-size:13px;">No games found. Maybe that\'s one to add! 👀</div>' : ""}
  `;
}

// Default show first category
showCategory("board");
</script>
</body>
</html>
