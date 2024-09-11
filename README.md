# galaxy_shot
pip install pygame
import pygame
import random

# Инициализация Pygame
pygame.init()

# Настройки экрана
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Космическая битва")

# Цвета
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)

# Игрок
player_size = 50
player_pos = [WIDTH // 2, HEIGHT - 2 * player_size]

# Противники
enemy_size = 50
enemy_pos = [random.randint(0, WIDTH - enemy_size), 0]
enemy_list = [enemy_pos]

# Пули
bullet_size = 5
bullet_list = []

# Игровые параметры
speed = 10
enemy_speed = 10
bullet_speed = 20

# Главный игровой цикл
game_over = False
clock = pygame.time.Clock()

def drop_enemies(enemy_list):
    if len(enemy_list) < 10:
        enemy_pos = [random.randint(0, WIDTH - enemy_size), 0]
        enemy_list.append(enemy_pos)

def update_enemy_positions(enemy_list):
    for index, enemy_pos in enumerate(enemy_list):
        if enemy_pos[1] >= 0 and enemy_pos[1] < HEIGHT:
            enemy_pos[1] += enemy_speed
        else:
            enemy_list.pop(index)

def bullet_collision(enemy_list, bullet_list):
    for bullet in bullet_list:
        for enemy in enemy_list:
            if (enemy[1] < bullet[1] < enemy[1] + enemy_size and
                    enemy[0] < bullet[0] < enemy[0] + enemy_size):
                bullet_list.remove(bullet)
                enemy_list.remove(enemy)

# Основной игровой цикл
while not game_over:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True

    # Управление игроком
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_pos[0] > 0:
        player_pos[0] -= speed
    if keys[pygame.K_RIGHT] and player_pos[0] < WIDTH - player_size:
        player_pos[0] += speed
    if keys[pygame.K_SPACE]:
        bullet_list.append([player_pos[0] + player_size // 2, player_pos[1]])

    # Обновление позиции противников и пуль
    drop_enemies(enemy_list)
    update_enemy_positions(enemy_list)

    # Обновление коллизий
    bullet_collision(enemy_list, bullet_list)

    # Рендеринг
    screen.fill(BLACK)
    pygame.draw.rect(screen, WHITE, (player_pos[0], player_pos[1], player_size, player_size))
    
    for enemy in enemy_list:
        pygame.draw.rect(screen, RED, (enemy[0], enemy[1], enemy_size, enemy_size))
    
    for bullet in bullet_list:
        pygame.draw.rect(screen, WHITE, (bullet[0], bullet[1], bullet_size, bullet_size))
        bullet[1] -= bullet_speed

    # Обновляем экран
    pygame.display.flip()

    # Задержка
    clock.tick(30)

pygame.quit()

