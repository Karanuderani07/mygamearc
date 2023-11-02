#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>

typedef struct {
    char name[20];
    int health;
    int maxHealth;
    int attack;
    int defense;
    int experience;
    int level;
    int numPotions;
} Character;

typedef struct {
    char name[20];
    int health;
    int attack;
} Monster;

void initCharacter(Character *player, char *name) {
    strcpy(player->name, name);
    player->maxHealth = 100;
    player->health = player->maxHealth;
    player->attack = 20;
    player->defense = 10;
    player->experience = 0;
    player->level = 1;
    player->numPotions = 3;
}

void initMonster(Monster *enemy) {
    char monsterNames[5][20] = {"Goblin", "Orc", "Skeleton", "Spider", "Dragon"};
    int index = rand() % 5;
    strcpy(enemy->name, monsterNames[index]);
    enemy->health = 50 + rand() % 51; // Random monster health between 50 and 100
    enemy->attack = 10 + rand() % 21; // Random monster attack between 10 and 30
}

void displayStats(Character player, Monster enemy) {
    printf("\n%s's Health: %d/%d | Level: %d | Experience: %d | Potions: %d\n", player.name, player.health, player.maxHealth, player.level, player.experience, player.numPotions);
    printf("%s's Health: %d\n", enemy.name, enemy.health);
}

int getPlayerAttackDamage(Character player) {
    return player.attack + rand() % 11; // Random attack damage between player's attack and (player's attack + 10)
}

int getEnemyAttackDamage(Monster enemy) {
    return enemy.attack + rand() % 11; // Random attack damage between enemy's attack and (enemy's attack + 10)
}

int main() {
    srand(time(NULL));

    Character player;
    Monster enemy;

    char playerName[20];
    printf("Enter your character's name: ");
    scanf("%s", playerName);

    initCharacter(&player, playerName);

    printf("Welcome, %s! Get ready for battle!\n", player.name);
    while (1) {
        initMonster(&enemy);
        printf("\n--- New Battle ---\n");
        displayStats(player, enemy);

        while (player.health > 0 && enemy.health > 0) {
            printf("1. Attack\n2. Defend\n3. Use Potion\n");
            int choice;
            scanf("%d", &choice);

            int playerDamage = 0, enemyDamage = 0;
            if (choice == 1) {
                playerDamage = getPlayerAttackDamage(player);
                enemy.health -= playerDamage;
                printf("You dealt %d damage to %s!\n", playerDamage, enemy.name);
            } else if (choice == 2) {
                printf("You defended and blocked the enemy's attack!\n");
            } else if (choice == 3 && player.numPotions > 0) {
                int potionHeal = 30 + rand() % 21; // Heal between 30 and 50 HP
                player.health += potionHeal;
                if (player.health > player.maxHealth) {
                    player.health = player.maxHealth;
                }
                printf("You used a healing potion and restored %d health!\n", potionHeal);
                player.numPotions--;
            } else {
                printf("Invalid choice! Try again.\n");
                continue;
            }

            if (enemy.health > 0) {
                enemyDamage = getEnemyAttackDamage(enemy);
                player.health -= enemyDamage;
                printf("%s dealt %d damage to you!\n", enemy.name, enemyDamage);
            }
            displayStats(player, enemy);
        }

        if (player.health <= 0) {
            printf("Game Over. You were defeated by %s.\n", enemy.name);
            break;
        } else {
            printf("Congratulations! You defeated %s and gained experience points.\n", enemy.name);
            player.experience += 20;
            if (player.experience >= 100) {
                player.experience -= 100;
                player.level++;
                player.maxHealth += 10;
                player.health = player.maxHealth;
                player.attack += 5;
                player.defense += 2;
                player.numPotions += 2;
                printf("Level Up! You are now level %d!\n", player.level);
            }
        }

        printf("\nDo you want to continue? (1 for Yes, 0 for No): ");
        int choice;
        scanf("%d", &choice);
        if (choice == 0) {
            printf("Thanks for playing! Goodbye!\n");
            break;
        }
    }

    return 0;
}
