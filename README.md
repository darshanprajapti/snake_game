#include <stdio.h>
#include <conio.h>  // For getch() function
#include <stdlib.h>
#include <windows.h>  // For Sleep() function

int gameOver;
int width = 20;
int height = 10;
int x, y, snackX, snackY, score;
int tailX[100], tailY[100];
int nTail;
enum eDirection { STOP = 0, LEFT, RIGHT, UP, DOWN };
enum eDirection dir;

void setup() {
    gameOver = 0;
    dir = STOP;
    x = width / 2;
    y = height / 2;
    snackX = rand() % width;
    snackY = rand() % height;
    score = 0;
}

void draw() {
    system("cls");  // Clear the console

    for (int i = 0; i < width + 2; i++)
        printf("#");
    printf("\n");

    for (int i = 0; i < height; i++) {
        for (int j = 0; j < width; j++) {
            if (j == 0)
                printf("#");
            if (i == y && j == x)
                printf("O");
            else if (i == snackY && j == snackX)
                printf("X");
            else {
                int isPrinted = 0;
                for (int k = 0; k < nTail; k++) {
                    if (tailX[k] == j && tailY[k] == i) {
                        printf("o");
                        isPrinted = 1;
                    }
                }
                if (!isPrinted)
                    printf(" ");
            }
            if (j == width - 1)
                printf("#");
        }
        printf("\n");
    }

    for (int i = 0; i < width + 2; i++)
        printf("#");
    printf("\n");

    printf("Score: %d\n", score);
}

void input() {
    if (_kbhit()) {
        switch (_getch()) {
        case 'a':
            dir = LEFT;
            break;
        case 'd':
            dir = RIGHT;
            break;
        case 'w':
            dir = UP;
            break;
        case 's':
            dir = DOWN;
            break;
        case 'x':
            gameOver = 1;
            break;
        }
    }
}

void algorithm() {
    int prevX = tailX[0];
    int prevY = tailY[0];
    int prev2X, prev2Y;
    tailX[0] = x;
    tailY[0] = y;

    for (int i = 1; i < nTail; i++) {
        prev2X = tailX[i];
        prev2Y = tailY[i];
        tailX[i] = prevX;
        tailY[i] = prevY;
        prevX = prev2X;
        prevY = prev2Y;
    }

    switch (dir) {
    case LEFT:
        x--;
        break;
    case RIGHT:
        x++;
        break;
    case UP:
        y--;
        break;
    case DOWN:
        y++;
        break;
    default:
        break;
    }

    if (x < 0 || x >= width || y < 0 || y >= height)
        gameOver = 1;

    for (int i = 0; i < nTail; i++) {
        if (tailX[i] == x && tailY[i] == y)
            gameOver = 1;
    }

    if (x == snackX && y == snackY) {
        score += 10;
        snackX = rand() % width;
        snackY = rand() % height;
        nTail++;
    }
}

int main() {
    setup();
    while (!gameOver) {
        draw();
        input();
        algorithm();
        Sleep(100);  // Pause for a short period to make the game playable
    }

    return 0;
}
