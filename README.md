// nmit-project
// Software Engineering Project
// Author - Vivek Sharma(1nt24cb060)
#include <stdio.h>
#include <conio.h>    // For _kbhit(), getch()
#include <stdlib.h>   // For rand(), system()
#include <windows.h>  // For Sleep()
#define WIDTH 40
#define HEIGHT 20

int i, j, score, gameOver;
int x, y, fruitX, fruitY, flag;

// Tail arrays
int tailX[100], tailY[100];
int nTail = 0;

// Function declarations
void setup();
void draw();
void input();
void logic();

int main() {
    setup();
    while (!gameOver) {
        draw();
        input();
        logic();
        Sleep(100); // Control speed
    }
    printf("\nGame Over! Final Score: %d\n", score);
    return 0;
}

void setup() {
    gameOver = 0;
    x = WIDTH / 2;
    y = HEIGHT / 2;
    fruitX = rand() % WIDTH;
    fruitY = rand() % HEIGHT;
    score = 0;
}

void draw() {
    system("cls"); // clear screen

    for (i = 0; i < WIDTH + 2; i++)
        printf("#");
    printf("\n");

    for (i = 0; i < HEIGHT; i++) {
        for (j = 0; j < WIDTH; j++) {
            if (j == 0)
                printf("#");
            if (i == y && j == x)
                printf("O"); // Snake head
            else if (i == fruitY && j == fruitX)
                printf("*"); // Fruit
            else {
                int print = 0;
                for (int k = 0; k < nTail; k++) {
                    if (tailX[k] == j && tailY[k] == i) {
                        printf("o");
                        print = 1;
                    }
                }
                if (!print) printf(" ");
            }
            if (j == WIDTH - 1)
                printf("#");
        }
        printf("\n");
    }

    for (i = 0; i < WIDTH + 2; i++)
        printf("#");
    printf("\nScore: %d\n", score);
}

void input() {
    if (_kbhit()) {
        switch (_getch()) {
            case 'a': flag = 1; break; // left
            case 'd': flag = 2; break; // right
            case 'w': flag = 3; break; // up
            case 's': flag = 4; break; // down
            case 'x': gameOver = 1; break;
        }
    }
}

void logic() {
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

    switch (flag) {
        case 1: x--; break;
        case 2: x++; break;
        case 3: y--; break;
        case 4: y++; break;
        default: break;
    }

    // Hit wall
    if (x < 0 || x >= WIDTH || y < 0 || y >= HEIGHT)
        gameOver = 1;

    // Hit self
    for (int i = 0; i < nTail; i++)
        if (tailX[i] == x && tailY[i] == y)
            gameOver = 1;

    // Eat fruit
    if (x == fruitX && y == fruitY) {
        score += 10;
        fruitX = rand() % WIDTH;
        fruitY = rand() % HEIGHT;
        nTail++;
    }
}
