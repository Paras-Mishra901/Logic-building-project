#include <stdio.h>
#include <stdlib.h>

char board[3][3];
const char PLAYER = 'X';
const char COMPUTER = 'O';

void resetBoard();
void printBoard();
int checkFreeSpaces();
void playerMove();
void computerMove();
char checkWinner();
void printWinner(char);
int canWin(char, int*, int*);

int main() {
    char winner = ' ';
    resetBoard();
    printf("\nWelcome to Tic Tac Toe!\n");
    while(winner == ' ' && checkFreeSpaces() != 0) {
        printBoard();
        playerMove();
        winner = checkWinner();
        if(winner != ' ' || checkFreeSpaces() == 0) break;

        computerMove();
        winner = checkWinner();
        if(winner != ' ' || checkFreeSpaces() == 0) break;
    }

    printBoard();
    printWinner(winner);
    return 0;
}

// Initialize board
void resetBoard() {
    for(int i=0;i<3;i++)
        for(int j=0;j<3;j++)
            board[i][j] = ' ';
}

// Print board
void printBoard() {
    printf("\n %c | %c | %c \n", board[0][0], board[0][1], board[0][2]);
    printf("---|---|---\n");
    printf(" %c | %c | %c \n", board[1][0], board[1][1], board[1][2]);
    printf("---|---|---\n");
    printf(" %c | %c | %c \n", board[2][0], board[2][1], board[2][2]);
    printf("\n");
}

// Count free spaces
int checkFreeSpaces() {
    int freeSpaces = 0;
    for(int i=0;i<3;i++)
        for(int j=0;j<3;j++)
            if(board[i][j] == ' ') freeSpaces++;
    return freeSpaces;
}

// Player move
void playerMove() {
    int x, y;
    do {
        printf("Enter row #(1-3): "); scanf("%d", &x);
        printf("Enter column #(1-3): "); scanf("%d", &y);
        x--; y--;
        if(x<0 || x>2 || y<0 || y>2 || board[x][y]!=' ')
            printf("Invalid move! Try again.\n");
        else break;
    } while(1);
    board[x][y] = PLAYER;
}

// Computer move
void computerMove() {
    int x = -1, y = -1;
    // 1. Win if possible
    if(canWin(COMPUTER, &x, &y)) { board[x][y] = COMPUTER; return; }
    // 2. Block player
    if(canWin(PLAYER, &x, &y)) { board[x][y] = COMPUTER; return; }
    // 3. Take center
    if(board[1][1] == ' ') { board[1][1] = COMPUTER; return; }
    // 4. Take corner
    int corners[4][2] = {{0,0},{0,2},{2,0},{2,2}};
    for(int i=0;i<4;i++){
        int r = corners[i][0], c = corners[i][1];
        if(board[r][c] == ' ') { board[r][c] = COMPUTER; return; }
    }
    // 5. Take any side
    int sides[4][2] = {{0,1},{1,0},{1,2},{2,1}};
    for(int i=0;i<4;i++){
        int r = sides[i][0], c = sides[i][1];
        if(board[r][c] == ' ') { board[r][c] = COMPUTER; return; }
    }
}

// Check if a player can win in next move
int canWin(char sym, int *row, int *col) {
    for(int i=0;i<3;i++){
        for(int j=0;j<3;j++){
            if(board[i][j]==' '){
                board[i][j] = sym;
                if(checkWinner() == sym){
                    *row = i; *col = j;
                    board[i][j] = ' ';
                    return 1;
                }
                board[i][j] = ' ';
            }
        }
    }
    return 0;
}

// Check winner
char checkWinner() {
    // Rows & columns
    for(int i=0;i<3;i++){
        if(board[i][0]==board[i][1] && board[i][0]==board[i][2] && board[i][0]!=' ') return board[i][0];
        if(board[0][i]==board[1][i] && board[0][i]==board[2][i] && board[0][i]!=' ') return board[0][i];
    }
    // Diagonals
    if(board[0][0]==board[1][1] && board[0][0]==board[2][2] && board[0][0]!=' ') return board[0][0];
    if(board[0][2]==board[1][1] && board[0][2]==board[2][0] && board[0][2]!=' ') return board[0][2];
    return ' ';
}

// Print winner
void printWinner(char winner) {
    if(winner == PLAYER) printf("You won!\n");
    else if(winner == COMPUTER) printf("Computer won!\n");
    else printf("It's a tie!\n");
}
