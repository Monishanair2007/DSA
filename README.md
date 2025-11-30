// DSA ( c programming)
// a standard c program for making tic tac toe game which consist of 3x3 matrix
//program for mini project in c program in dsal
#include <stdio.h>
#include <stdlib.h>
// Struct Definition
struct Node {
    char board[3][3];
    char player;
    struct Node* children[9];
    int child_count;
};
// Utility: Print Board
void printBoard(char board[3][3]) {
    int i, j;
    printf("\nCurrent Board:\n");
    for (i = 0; i < 3; i++) {
        for (j = 0; j < 3; j++) {
            printf(" %c ", board[i][j] ? board[i][j] : '.');
        }
        printf("\n");
    }
}
// Check Win
int checkWin(char board[3][3], char player) {
    int i;
    for (i = 0; i < 3; i++) {
        if ((board[i][0] == player && board[i][1] == player && board[i][2] == player) ||
            (board[0][i] == player && board[1][i] == player && board[2][i] == player))
            return 1;
    }
    if ((board[0][0] == player && board[1][1] == player && board[2][2] == player) ||
        (board[0][2] == player && board[1][1] == player && board[2][0] == player))
        return 1;
    return 0;
}
// Check Full
int checkFull(char board[3][3]) {
    int i, j;
    for (i = 0; i < 3; i++)
        for (j = 0; j < 3; j++)
            if (!board[i][j])
                return 0;
    return 1;
}
// Manual Board Copy
void copyBoard(char dest[3][3], char src[3][3]) {
    int i, j;
    for (i = 0; i < 3; i++)
        for (j = 0; j < 3; j++)
            dest[i][j] = src[i][j];
}

// Create Node
struct Node* createNode(char board[3][3], char player) {
    struct Node* node = (struct Node*)malloc(sizeof(struct Node));
    copyBoard(node->board, board);
    node->player = player;
    node->child_count = 0;
    int i;
    for (i = 0; i < 9; i++)
        node->children[i] = NULL;
    return node;
}
// Generate Children
void generateChildren(struct Node* node) {
    if (checkWin(node->board, 'X') || checkWin(node->board, 'O') || checkFull(node->board))
        return;

    int i, j;
    for (i = 0; i < 3; i++) {
        for (j = 0; j < 3; j++) {
            if (!node->board[i][j]) {
                char newBoard[3][3];
                copyBoard(newBoard, node->board);
                newBoard[i][j] = node->player;
                char nextPlayer = (node->player == 'X') ? 'O' : 'X';
                struct Node* child = createNode(newBoard, nextPlayer);
                node->children[node->child_count++] = child;
                generateChildren(child);
            }
        }
    }
}
// Preorder Traversal
void preorderTraversal(struct Node* node) {
    if (node == NULL) return;

    printBoard(node->board);
    printf("Player: %c | Children: %d\n\n", node->player, node->child_count);

    int i;
    for (i = 0; i < node->child_count; i++) {
        preorderTraversal(node->children[i]);
    }
}
// Count Terminal States
int countTerminal(struct Node* node) {
    if (node == NULL) return 0;
    if (node->child_count == 0) return 1;

    int count = 0;
    int i;
    for (i = 0; i < node->child_count; i++) {
        count += countTerminal(node->children[i]);
    }
    return count;
}
// Main Functio
int main() {
    int games, g;
    printf("How many full games do you want to play? ");
    scanf("%d", &games);

    for (g = 0; g < games; g++) {
        printf("\nGame %d:\n", g + 1);
        char board[3][3] = {{0}};
        char currentPlayer = 'X';
        int moves = 0;

        while (!checkWin(board, 'X') && !checkWin(board, 'O') && moves < 9) {
            printBoard(board);
            int row, col;

            while (1) {
                printf("Player %c, enter row and column (0-2): ", currentPlayer);
                scanf("%d %d", &row, &col);

                if (row >= 0 && row <= 2 && col >= 0 && col <= 2 && !board[row][col]) {
                    board[row][col] = currentPlayer;
                    break;
                } else {
                    printf("Invalid move. Try again.\n");
                }
            }

            currentPlayer = (currentPlayer == 'X') ? 'O' : 'X';
            moves++;
        }

        printBoard(board);
        if (checkWin(board, 'X'))
            printf("Player X wins!\n");
        else if (checkWin(board, 'O'))
            printf("Player O wins!\n");
        else
            printf("It's a draw!\n");

        struct Node* root = createNode(board, currentPlayer);
        generateChildren(root);

        printf("\nPreorder Traversal of Game Tree:\n");
        preorderTraversal(root);

        int terminalCount = countTerminal(root);
        printf("Total Terminal States from this board: %d\n", terminalCount);
    }

    return 0;
}
