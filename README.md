# Flappy-Bird-Game
This C-program code creates a basic "Flappy Bird" game using the 'graphics.h' Library.
#include <graphics.h>
#include <conio.h> // For getch()
#include <stdlib.h>
#include <stdio.h>
#include <time.h>

#define SCREEN_WIDTH 640
#define SCREEN_HEIGHT 480
#define GRAVITY 0.5
#define FLAP_FORCE 10
#define PIPE_WIDTH 50
#define PIPE_HEIGHT 300
#define PIPE_DISTANCE 200
#define PIPE_SPEED 5

typedef struct {
    int x, y;
    float velocity;
} Bird;

typedef struct {
    int x, y;
} Pipe;

Bird bird;
Pipe pipes[3];
int score = 0;

void init() {
    int gd = DETECT, gm;
    initgraph(&gd, &gm, "");

    // Initialize random seed
    srand(time(NULL));

    bird.x = SCREEN_WIDTH / 4;
    bird.y = SCREEN_HEIGHT / 2;
    bird.velocity = 0;

    for (int i = 0; i < 3; ++i) {
        pipes[i].x = SCREEN_WIDTH + i * PIPE_DISTANCE;
        pipes[i].y = rand() % (SCREEN_HEIGHT - PIPE_HEIGHT);
    }
}

void handleEvents() {
    if (kbhit()) {
        char ch = getch();
        if (ch == ' ') {
            bird.velocity = -FLAP_FORCE;
        }
    }
}

void update() {
    bird.velocity += GRAVITY;
    bird.y += (int)bird.velocity;

    for (int i = 0; i < 3; ++i) {
        pipes[i].x -= PIPE_SPEED;
        if (pipes[i].x + PIPE_WIDTH < 0) {
            pipes[i].x = SCREEN_WIDTH;
            pipes[i].y = rand() % (SCREEN_HEIGHT - PIPE_HEIGHT);
            score++;
        }
    }

    if (bird.y < 0 || bird.y + 20 > SCREEN_HEIGHT) {
        printf("Game Over! Your score: %d\n", score);
        getch();
        exit(0);
    }

    for (int i = 0; i < 3; ++i) {
        if (bird.x + 20 > pipes[i].x && bird.x < pipes[i].x + PIPE_WIDTH) {
            if (bird.y < pipes[i].y || bird.y + 20 > pipes[i].y + PIPE_HEIGHT) {
                printf("Game Over! Your score: %d\n", score);
                getch();
                exit(0);
            }
        }
    }
}

void render() {
    cleardevice();

    // Draw bird
    setcolor(WHITE);
    rectangle(bird.x, bird.y, bird.x + 20, bird.y + 20);

    // Draw pipes
    for (int i = 0; i < 3; ++i) {
        setfillstyle(SOLID_FILL, WHITE);
        bar(pipes[i].x, 0, pipes[i].x + PIPE_WIDTH, pipes[i].y);   
        bar(pipes[i].x, pipes[i].y + PIPE_HEIGHT, pipes[i].x + PIPE_WIDTH, SCREEN_HEIGHT);
    }

    // Render score
    char scoreText[50];
    snprintf(scoreText, sizeof(scoreText), "Score: %d", score);
    setcolor(WHITE);
    outtextxy(10, 10, scoreText);

    delay(10);
}

void cleanUp() {
    closegraph();
}

int main() {
    init();

    while (1) {
        handleEvents();
        update();
        render();
    }

    cleanUp();
    return 0;
}
