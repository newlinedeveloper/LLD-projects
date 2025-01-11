### Low-Level Design (LLD) for Tic Tac Toe Game

#### Requirements:
- The game is played on a 3x3 grid.
- Two players, one using `X` and the other `O`.
- Players take turns to place their symbol in an empty cell.
- The first player to align three of their symbols in a row, column, or diagonal wins.
- If all cells are filled without a winner, the game ends in a draw.

#### Core Components:
1. **Game**: Manages the game loop and checks for winners or a draw.
2. **Board**: Represents the game grid.
3. **Player**: Represents a player.
4. **Move**: Represents a player's move.
5. **Display**: Strategy pattern for displaying the board.

#### Design Pattern Used:
- **Strategy Pattern**: Used for different display strategies (e.g., console display, GUI).

---

### Class Diagram:

```plaintext
Game
  - board: Board
  - players: [Player]
  - currentPlayerIndex: int
  + start_game()
  + switch_player()
  + check_winner()
  + check_draw()

Board
  - grid: 2D list of str
  + display(display_strategy: Display)
  + make_move(move: Move) -> bool

Player
  - name: str
  - symbol: str

Move
  - row: int
  - col: int
  - player: Player

Display (Interface)
  + show(grid: 2D list of str)
```

---

### Python Code

```python
class Board:
    def __init__(self):
        self.grid = [[' ' for _ in range(3)] for _ in range(3)]

    def display(self, display_strategy):
        display_strategy.show(self.grid)

    def make_move(self, move):
        if self.grid[move.row][move.col] == ' ':
            self.grid[move.row][move.col] = move.player.symbol
            return True
        return False

    def check_winner(self):
        for i in range(3):
            if self.grid[i][0] == self.grid[i][1] == self.grid[i][2] != ' ':
                return True
            if self.grid[0][i] == self.grid[1][i] == self.grid[2][i] != ' ':
                return True
        if self.grid[0][0] == self.grid[1][1] == self.grid[2][2] != ' ':
            return True
        if self.grid[0][2] == self.grid[1][1] == self.grid[2][0] != ' ':
            return True
        return False

    def is_full(self):
        return all(cell != ' ' for row in self.grid for cell in row)


class Player:
    def __init__(self, name, symbol):
        self.name = name
        self.symbol = symbol


class Move:
    def __init__(self, row, col, player):
        self.row = row
        self.col = col
        self.player = player


class Display:
    def show(self, grid):
        raise NotImplementedError


class ConsoleDisplay(Display):
    def show(self, grid):
        for row in grid:
            print("|".join(row))
            print("-" * 5)


class Game:
    def __init__(self, player1, player2):
        self.board = Board()
        self.players = [player1, player2]
        self.currentPlayerIndex = 0

    def switch_player(self):
        self.currentPlayerIndex = 1 - self.currentPlayerIndex

    def start_game(self):
        console_display = ConsoleDisplay()
        while True:
            self.board.display(console_display)
            current_player = self.players[self.currentPlayerIndex]
            print(f"{current_player.name}'s turn ({current_player.symbol})")
            row, col = map(int, input("Enter row and column (0-2): ").split())
            move = Move(row, col, current_player)

            if self.board.make_move(move):
                if self.board.check_winner():
                    self.board.display(console_display)
                    print(f"{current_player.name} wins!")
                    break
                elif self.board.is_full():
                    self.board.display(console_display)
                    print("It's a draw!")
                    break
                self.switch_player()
            else:
                print("Invalid move. Try again.")

# Example usage
player1 = Player("Alice", "X")
player2 = Player("Bob", "O")
game = Game(player1, player2)
game.start_game()
```

---

### Golang Code

```go
package main

import (
	"fmt"
)

type Board struct {
	grid [3][3]string
}

func NewBoard() *Board {
	return &Board{grid: [3][3]string{
		{" ", " ", " "},
		{" ", " ", " "},
		{" ", " ", " "},
	}}
}

func (b *Board) Display(displayStrategy Display) {
	displayStrategy.Show(b.grid)
}

func (b *Board) MakeMove(row, col int, symbol string) bool {
	if b.grid[row][col] == " " {
		b.grid[row][col] = symbol
		return true
	}
	return false
}

func (b *Board) CheckWinner() bool {
	for i := 0; i < 3; i++ {
		if b.grid[i][0] == b.grid[i][1] && b.grid[i][1] == b.grid[i][2] && b.grid[i][0] != " " {
			return true
		}
		if b.grid[0][i] == b.grid[1][i] && b.grid[1][i] == b.grid[2][i] && b.grid[0][i] != " " {
			return true
		}
	}
	if b.grid[0][0] == b.grid[1][1] && b.grid[1][1] == b.grid[2][2] && b.grid[0][0] != " " {
		return true
	}
	if b.grid[0][2] == b.grid[1][1] && b.grid[1][1] == b.grid[2][0] && b.grid[0][2] != " " {
		return true
	}
	return false
}

func (b *Board) IsFull() bool {
	for _, row := range b.grid {
		for _, cell := range row {
			if cell == " " {
				return false
			}
		}
	}
	return true
}

type Player struct {
	Name   string
	Symbol string
}

type Move struct {
	Row, Col int
	Player   Player
}

type Display interface {
	Show(grid [3][3]string)
}

type ConsoleDisplay struct{}

func (d ConsoleDisplay) Show(grid [3][3]string) {
	for _, row := range grid {
		fmt.Println(row)
	}
}

type Game struct {
	board           *Board
	players         [2]Player
	currentPlayerIndex int
}

func NewGame(player1, player2 Player) *Game {
	return &Game{
		board: NewBoard(),
		players: [2]Player{player1, player2},
	}
}

func (g *Game) SwitchPlayer() {
	g.currentPlayerIndex = 1 - g.currentPlayerIndex
}

func (g *Game) StartGame() {
	consoleDisplay := ConsoleDisplay{}
	for {
		g.board.Display(consoleDisplay)
		currentPlayer := g.players[g.currentPlayerIndex]
		fmt.Printf("%s's turn (%s):\n", currentPlayer.Name, currentPlayer.Symbol)
		var row, col int
		fmt.Print("Enter row and column (0-2): ")
		fmt.Scan(&row, &col)

		if g.board.MakeMove(row, col, currentPlayer.Symbol) {
			if g.board.CheckWinner() {
				g.board.Display(consoleDisplay)
				fmt.Printf("%s wins!\n", currentPlayer.Name)
				break
			} else if g.board.IsFull() {
				g.board.Display(consoleDisplay)
				fmt.Println("It's a draw!")
				break
			}
			g.SwitchPlayer()
		} else {
			fmt.Println("Invalid move. Try again.")
		}
	}
}

func main() {
	player1 := Player{Name: "Alice", Symbol: "X"}
	player2 := Player{Name: "Bob", Symbol: "O"}
	game := NewGame(player1, player2)
	game.StartGame()
}
```

---
