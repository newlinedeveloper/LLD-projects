### Low-Level Design (LLD) for Snake and Ladder Game

#### Requirements:
- The game is played on a board with 100 squares.
- Players take turns to roll a dice and move their piece forward.
- If a player lands on the bottom of a ladder, they move up to the top of the ladder.
- If a player lands on the mouth of a snake, they move down to the tail of the snake.
- The first player to reach square 100 wins the game.

#### Core Components:
1. **Game**: Manages the game flow, players, and checks for winning conditions.
2. **Board**: Represents the game board with snakes and ladders.
3. **Player**: Represents a player.
4. **Dice**: Represents the dice used to generate random moves.
5. **Cell**: Represents each cell on the board, which may contain a snake or ladder.
6. **Display**: Strategy pattern for displaying the game board.

#### Design Pattern Used:
- **Strategy Pattern**: Used for different display strategies (e.g., console display, GUI).
- **Singleton Pattern**: Used for the dice to ensure only one instance is used across the game.

---

### Class Diagram:

```plaintext
Game
  - board: Board
  - players: [Player]
  + start_game()
  + move_player(player: Player)

Board
  - cells: [Cell]
  + initialize_board(snakes: [int], ladders: [int])
  + get_destination(position: int) -> int

Cell
  - position: int
  - jump: int (0 if no snake or ladder)

Player
  - name: str
  - position: int

Dice
  + roll() -> int

Display (Interface)
  + show(players: [Player], board: Board)
```

---

### Python Code

```python
import random

class Dice:
    @staticmethod
    def roll():
        return random.randint(1, 6)


class Cell:
    def __init__(self, position):
        self.position = position
        self.jump = 0  # 0 means no snake or ladder


class Board:
    def __init__(self):
        self.cells = [Cell(i) for i in range(101)]

    def initialize_board(self, snakes, ladders):
        for start, end in snakes.items():
            self.cells[start].jump = end - start
        for start, end in ladders.items():
            self.cells[start].jump = end - start

    def get_destination(self, position):
        cell = self.cells[position]
        return position + cell.jump


class Player:
    def __init__(self, name):
        self.name = name
        self.position = 0


class Display:
    def show(self, players, board):
        raise NotImplementedError


class ConsoleDisplay(Display):
    def show(self, players, board):
        for player in players:
            print(f"{player.name} is at position {player.position}")


class Game:
    def __init__(self, players, board, display):
        self.players = players
        self.board = board
        self.display = display

    def move_player(self, player):
        dice_value = Dice.roll()
        new_position = player.position + dice_value
        if new_position > 100:
            return
        new_position = self.board.get_destination(new_position)
        player.position = new_position

    def start_game(self):
        while True:
            for player in self.players:
                self.move_player(player)
                self.display.show(self.players, self.board)
                if player.position == 100:
                    print(f"{player.name} wins!")
                    return


# Example usage
snakes = {16: 6, 47: 26, 49: 11, 56: 53, 62: 19, 64: 60, 87: 24, 93: 73, 95: 75, 98: 78}
ladders = {1: 38, 4: 14, 9: 31, 21: 42, 28: 84, 36: 44, 51: 67, 71: 91, 80: 100}

board = Board()
board.initialize_board(snakes, ladders)

player1 = Player("Alice")
player2 = Player("Bob")
players = [player1, player2]

display = ConsoleDisplay()
game = Game(players, board, display)
game.start_game()
```

---

### Golang Code

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

type Cell struct {
	position int
	jump     int // 0 means no snake or ladder
}

type Board struct {
	cells [101]Cell
}

func NewBoard() *Board {
	b := &Board{}
	for i := 0; i <= 100; i++ {
		b.cells[i] = Cell{position: i, jump: 0}
	}
	return b
}

func (b *Board) InitializeBoard(snakes, ladders map[int]int) {
	for start, end := range snakes {
		b.cells[start].jump = end - start
	}
	for start, end := range ladders {
		b.cells[start].jump = end - start
	}
}

func (b *Board) GetDestination(position int) int {
	return position + b.cells[position].jump
}

type Player struct {
	name     string
	position int
}

type Dice struct{}

func (d *Dice) Roll() int {
	rand.Seed(time.Now().UnixNano())
	return rand.Intn(6) + 1
}

type Display interface {
	Show(players []*Player)
}

type ConsoleDisplay struct{}

func (d ConsoleDisplay) Show(players []*Player) {
	for _, player := range players {
		fmt.Printf("%s is at position %d\n", player.name, player.position)
	}
}

type Game struct {
	board    *Board
	players  []*Player
	display  Display
}

func NewGame(players []*Player, board *Board, display Display) *Game {
	return &Game{
		board:   board,
		players: players,
		display: display,
	}
}

func (g *Game) MovePlayer(player *Player) {
	dice := &Dice{}
	diceValue := dice.Roll()
	newPosition := player.position + diceValue
	if newPosition > 100 {
		return
	}
	newPosition = g.board.GetDestination(newPosition)
	player.position = newPosition
}

func (g *Game) StartGame() {
	for {
		for _, player := range g.players {
			g.MovePlayer(player)
			g.display.Show(g.players)
			if player.position == 100 {
				fmt.Printf("%s wins!\n", player.name)
				return
			}
		}
	}
}

func main() {
	snakes := map[int]int{16: 6, 47: 26, 49: 11, 56: 53, 62: 19, 64: 60, 87: 24, 93: 73, 95: 75, 98: 78}
	ladders := map[int]int{1: 38, 4: 14, 9: 31, 21: 42, 28: 84, 36: 44, 51: 67, 71: 91, 80: 100}

	board := NewBoard()
	board.InitializeBoard(snakes, ladders)

	player1 := &Player{name: "Alice"}
	player2 := &Player{name: "Bob"}
	players := []*Player{player1, player2}

	display := ConsoleDisplay{}
	game := NewGame(players, board, display)
	game.StartGame()
}
```

---
