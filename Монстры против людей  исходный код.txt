#include "raylib.h"
#include <stdlib.h>
#include <time.h>

typedef struct {
    const char* name;
    int health;
    int maxHealth;
    int attack;
    int defense;
} Character;

void DrawHealthBar(int x, int y, int width, int height, int health, int maxHealth, Color color) {
    DrawRectangle(x, y, width, height, GRAY);
    float ratio = (float)health / maxHealth;
    DrawRectangle(x, y, (int)(width * ratio), height, color);
    DrawRectangleLines(x, y, width, height, BLACK);
}

int CalculateDamage(int attackerAtk, int defenderDef) {
    int raw = attackerAtk - defenderDef + (GetRandomValue(-2, 2));
    return raw > 0 ? raw : 1;
}

int main() {
    InitWindow(800, 600, "Monster Battle - Raylib C Version");
    SetTargetFPS(60);
    srand(time(NULL));

    Character hero = {"Hero", 100, 100, 20, 5};
    Character monster = {"Monster", 100, 100, 15, 3};

    bool heroTurn = true;
    bool gameOver = false;
    bool heroWon = false;

    while (!WindowShouldClose()) {
        if (!gameOver && IsKeyPressed(KEY_SPACE)) {
            if (heroTurn) {
                int dmg = CalculateDamage(hero.attack, monster.defense);
                monster.health -= dmg;
                if (monster.health <= 0) {
                    monster.health = 0;
                    gameOver = true;
                    heroWon = true;
                }
            } else {
                int dmg = CalculateDamage(monster.attack, hero.defense);
                hero.health -= dmg;
                if (hero.health <= 0) {
                    hero.health = 0;
                    gameOver = true;
                    heroWon = false;
                }
            }
            heroTurn = !heroTurn;
        }

        if (gameOver && IsKeyPressed(KEY_R)) {
            hero.health = hero.maxHealth;
            monster.health = monster.maxHealth;
            heroTurn = true;
            gameOver = false;
        }

        BeginDrawing();
        ClearBackground(RAYWHITE);

        DrawText("Monster Battle", 290, 20, 30, DARKBLUE);

        DrawText(TextFormat("%s HP: %d", hero.name, hero.health), 50, 100, 20, DARKGREEN);
        DrawHealthBar(50, 130, 300, 20, hero.health, hero.maxHealth, GREEN);

        DrawText(TextFormat("%s HP: %d", monster.name, monster.health), 450, 100, 20, MAROON);
        DrawHealthBar(450, 130, 300, 20, monster.health, monster.maxHealth, RED);

        if (!gameOver) {
            DrawText(heroTurn ? "Your Turn (press SPACE to attack)" : "Monster's Turn (press SPACE to continue)", 180, 300, 20, BLACK);
        } else {
            DrawText(heroWon ? "You Won! Press R to Restart." : "You Lost! Press R to Retry.", 250, 300, 20, DARKPURPLE);
        }

        EndDrawing();
    }

    CloseWindow();
    return 0;
}
