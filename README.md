import random

class BattleshipGame:
    def __init__(self, size=10):
        self.size = size
        self.player_board = [['~' for _ in range(size)] for _ in range(size)]
        self.computer_board = [['~' for _ in range(size)] for _ in range(size)]
        self.ships = {'Carrier': 5, 'Battleship': 4, 'Cruiser': 3, 'Submarine': 3, 'Destroyer': 2}
        self.place_ships(self.player_board)
        self.place_ships(self.computer_board)
    
    def place_ships(self, board):
        for ship, length in self.ships.items():
            placed = False
            while not placed:
                x = random.randint(0, self.size-1)
                y = random.randint(0, self.size-1)
                orientation = random.choice(['horizontal', 'vertical'])
                
                if self.can_place_ship(board, x, y, length, orientation):
                    for i in range(length):
                        if orientation == 'horizontal':
                            board[x][y+i] = 'S'
                        else:
                            board[x+i][y] = 'S'
                    placed = True
    
    def can_place_ship(self, board, x, y, length, orientation):
        if orientation == 'horizontal':
            if y + length > self.size:
                return False
            return all(board[x][y+i] == '~' for i in range(length))
        else:
            if x + length > self.size:
                return False
            return all(board[x+i][y] == '~' for i in range(length))
    
    def print_board(self, board, hide_ships=False):
        print('  ' + ' '.join(str(i) for i in range(self.size)))
        for i in range(self.size):
            print(str(i) + ' ', end='')
            for j in range(self.size):
                if hide_ships and board[i][j] == 'S':
                    print('~ ', end='')
                else:
                    print(board[i][j] + ' ', end='')
            print()
    
    def player_turn(self):
        print("\nSeu tabuleiro:")
        self.print_board(self.player_board)
        print("\nTabuleiro do computador:")
        self.print_board(self.computer_board, hide_ships=True)
        
        while True:
            try:
                x = int(input("Digite a linha para atacar (0-9): "))
                y = int(input("Digite a coluna para atacar (0-9): "))
                
                if not (0 <= x < self.size and 0 <= y < self.size):
                    print("Coordenadas fora do tabuleiro!")
                    continue
                
                if self.computer_board[x][y] in ['X', 'O']:
                    print("Você já atacou esta posição!")
                    continue
                
                if self.computer_board[x][y] == 'S':
                    print("Acertou um navio!")
                    self.computer_board[x][y] = 'X'
                else:
                    print("Água!")
                    self.computer_board[x][y] = 'O'
                
                break
            except ValueError:
                print("Por favor, digite números válidos!")

    def computer_turn(self):
        x, y = random.randint(0, self.size-1), random.randint(0, self.size-1)
        while self.player_board[x][y] in ['X', 'O']:
            x, y = random.randint(0, self.size-1), random.randint(0, self.size-1)
        
        if self.player_board[x][y] == 'S':
            print(f"O computador acertou seu navio em ({x}, {y})!")
            self.player_board[x][y] = 'X'
        else:
            print(f"O computador atirou em ({x}, {y}) e errou.")
            self.player_board[x][y] = 'O'
    
    def check_win(self, board):
        return all(cell != 'S' for row in board for cell in row)
    
    def play(self):
        print("=== BATALHA NAVAL ===")
        while True:
            self.player_turn()
            if self.check_win(self.computer_board):
                print("Parabéns! Você venceu!")
                break
            
            self.computer_turn()
            if self.check_win(self.player_board):
                print("O computador venceu!")
                break

if __name__ == "__main__":
    game = BattleshipGame()
    game.play()