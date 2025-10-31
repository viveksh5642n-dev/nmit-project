// nmit-project
// Software Engineering Project
// Author - Vivek Sharma(1nt24cb060)
#include <stdio.h>
#include <stdlib.h>
#include <conio.h>   // Already has _kbhit()
#include <unistd.h>  // For usleep()

#define WIDTH 40
#define HEIGHT 20

int i, j, score, gameOver;
int x, y, fruitX, fruitY, flag;
int tailX[100], tailY[100];
int nTail = 0;

void setup();
void draw();
void input();
void logic();

void delay(int ms) {
    usleep(ms * 1000); // Replace Sleep()
}

int main() {
    setup();
    while (!gameOver) {
        draw();
        input();
        logic();
        delay(100); // Adjust game speed (lower = faster)
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
    system("clear"); // Works on Android/Linux

    for (i = 0; i < WIDTH + 2; i++)
        printf("#");
    printf("\n");

    for (i = 0; i < HEIGHT; i++) {
        for (j = 0; j < WIDTH; j++) {
            if (j == 0)
                printf("#");
            if (i == y && j == x)
                printf("O");
            else if (i == fruitY && j == fruitX)
                printf("*");
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
    if (_kbhit()) { // Uses conio.h built-in
        switch (getch()) {
            case 'a': flag = 1; break;
            case 'd': flag = 2; break;
            case 'w': flag = 3; break;
            case 's': flag = 4; break;
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
    }

    if (x < 0 || x >= WIDTH || y < 0 || y >= HEIGHT)
        gameOver = 1;

    for (int i = 0; i < nTail; i++)
        if (tailX[i] == x && tailY[i] == y)
            gameOver = 1;

    if (x == fruitX && y == fruitY) {
        score += 10;
        fruitX = rand() % WIDTH;
        fruitY = rand() % HEIGHT;
        nTail++;
    }
}
