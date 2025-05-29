#include "raylib.h"
#include <stdbool.h>

#define CELL_SIZE 100
#define PADDING 10
#define SCREEN_WIDTH (CELL_SIZE * 3 + PADDING * 4)
#define SCREEN_HEIGHT (CELL_SIZE * 3 + PADDING * 4)

char board[9];
char currentPlayer = 'X';
bool gameOver = false;
char winner = ' ';

void ResetBoard() {
    for (int i = 0; i < 9; i++) board[i] = ' ';
    currentPlayer = 'X';
    gameOver = false;
    winner = ' ';
}

bool CheckWin(char player) {
    int combos[8][3] = {
        {0, 1, 2}, {3, 4, 5}, {6, 7, 8},
        {0, 3, 6}, {1, 4, 7}, {2, 5, 8},
        {0, 4, 8}, {2, 4, 6}
    };
    for (int i = 0; i < 8; i++) {
        if (board[combos[i][0]] == player &&
            board[combos[i][1]] == player &&
            board[combos[i][2]] == player) {
            return true;
        }
    }
    return false;
}

bool IsDraw() {
    for (int i = 0; i < 9; i++) {
        if (board[i] == ' ') return false;
    }
    return true;
}

int GetCellIndex(int x, int y) {
    int row = y / (CELL_SIZE + PADDING);
    int col = x / (CELL_SIZE + PADDING);
    return row * 3 + col;
}

void DrawBoard() {
    for (int i = 0; i < 9; i++) {
        int row = i / 3;
        int col = i % 3;
        int x = PADDING + col * (CELL_SIZE + PADDING);
        int y = PADDING + row * (CELL_SIZE + PADDING);

        DrawRectangle(x, y, CELL_SIZE, CELL_SIZE, LIGHTGRAY);
        if (board[i] == 'X') {
            DrawLine(x + 20, y + 20, x + CELL_SIZE - 20, y + CELL_SIZE - 20, RED);
            DrawLine(x + CELL_SIZE - 20, y + 20, x + 20, y + CELL_SIZE - 20, RED);
        } else if (board[i] == 'O') {
            DrawCircleLines(x + CELL_SIZE / 2, y + CELL_SIZE / 2, CELL_SIZE / 2 - 20, BLUE);
        }
    }
}

int main() {
    InitWindow(SCREEN_WIDTH, SCREEN_HEIGHT + 60, "Tic-Tac-Toe");
    SetTargetFPS(60);

    ResetBoard();

    while (!WindowShouldClose()) {
        if (!gameOver && IsMouseButtonPressed(MOUSE_LEFT_BUTTON)) {
            Vector2 mouse = GetMousePosition();
            int cell = GetCellIndex(mouse.x, mouse.y);
            if (cell >= 0 && cell < 9 && board[cell] == ' ') {
                board[cell] = currentPlayer;
                if (CheckWin(currentPlayer)) {
                    gameOver = true;
                    winner = currentPlayer;
                } else if (IsDraw()) {
                    gameOver = true;
                } else {
                    currentPlayer = (currentPlayer == 'X') ? 'O' : 'X';
                }
            }
        }

        if (gameOver && IsKeyPressed(KEY_R)) {
            ResetBoard();
        }

        BeginDrawing();
        ClearBackground(RAYWHITE);
        DrawBoard();

        if (gameOver) {
            if (winner != ' ') {
                DrawText(TextFormat("%c wins! Press R to restart.", winner), 10, SCREEN_HEIGHT + 10, 20, GREEN);
            } else {
                DrawText("Draw! Press R to restart.", 10, SCREEN_HEIGHT + 10, 20, ORANGE);
            }
        } else {
            DrawText(TextFormat("Player: %c", currentPlayer), 10, SCREEN_HEIGHT + 10, 20, DARKGRAY);
        }

        EndDrawing();
    }

    CloseWindow();
    return 0;
}
