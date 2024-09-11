import pygame
import random

# Initialize pygame
pygame.init()

# Screen dimensions
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("DestroyerFalls")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 128, 255)

# FPS (Frames per second)
FPS = 60

# Player settings
PLAYER_WIDTH = 50
PLAYER_HEIGHT = 50
player_color = BLUE
player_x = SCREEN_WIDTH // 2 - PLAYER_WIDTH // 2
player_y = SCREEN_HEIGHT - PLAYER_HEIGHT  # Start on the ground
player_speed = 5
player_vel_y = 0
player_jump_height = 15
gravity = 1
is_jumping = False
ground_level = SCREEN_HEIGHT - PLAYER_HEIGHT
sword_swinging = False
player_health = 100
sword_damage = 25

# Sword settings
SWORD_WIDTH = 10
SWORD_HEIGHT = 40
sword_color = RED

# Enemy settings
enemy_color = (255, 0, 0)
enemy_size = 40
enemy_speed = 2
enemy_list = []
enemy_count = 5

# Clock to control frame rate
clock = pygame.time.Clock()

# Font
font = pygame.font.SysFont(None, 40)

def spawn_enemies():
    for _ in range(enemy_count):
        enemy_x = random.randint(0, SCREEN_WIDTH - enemy_size)
        enemy_y = random.randint(50, 400)  # Enemies don't spawn too close to the ground
        enemy_list.append([enemy_x, enemy_y])

def draw_player():
    pygame.draw.rect(screen, player_color, (player_x, player_y, PLAYER_WIDTH, PLAYER_HEIGHT))
    if sword_swinging:
        draw_sword()

def draw_sword():
    sword_x = player_x + PLAYER_WIDTH
    sword_y = player_y + PLAYER_HEIGHT // 2 - SWORD_HEIGHT // 2
    pygame.draw.rect(screen, sword_color, (sword_x, sword_y, SWORD_WIDTH, SWORD_HEIGHT))

def draw_enemies():
    for enemy in enemy_list:
        pygame.draw.rect(screen, enemy_color, (enemy[0], enemy[1], enemy_size, enemy_size))

def move_enemies():
    for enemy in enemy_list:
        if enemy[0] < player_x:
            enemy[0] += enemy_speed
        elif enemy[0] > player_x:
            enemy[0] -= enemy_speed

def check_collisions():
    global player_health
    for enemy in enemy_list:
        if (player_x < enemy[0] < player_x + PLAYER_WIDTH or player_x < enemy[0] + enemy_size < player_x + PLAYER_WIDTH) \
           and (player_y < enemy[1] < player_y + PLAYER_HEIGHT or player_y < enemy[1] + enemy_size < player_y + PLAYER_HEIGHT):
            player_health -= 1  # Player loses health on collision with enemy

        # Check if sword hits an enemy
        if sword_swinging:
            sword_x = player_x + PLAYER_WIDTH
            sword_y = player_y + PLAYER_HEIGHT // 2 - SWORD_HEIGHT // 2
            if (sword_x < enemy[0] + enemy_size and sword_x + SWORD_WIDTH > enemy[0]) \
               and (sword_y < enemy[1] + enemy_size and sword_y + SWORD_HEIGHT > enemy[1]):
                enemy_list.remove(enemy)

def draw_health():
    health_text = font.render(f"Health: {player_health}", True, BLACK)
    screen.blit(health_text, (10, 10))

def game_over():
    screen.fill(WHITE)
    if player_health <= 0:
        message = "Game Over! You Lost!"
    else:
        message = "You Win!"
    game_over_text = font.render(message, True, RED)
    screen.blit(game_over_text, (SCREEN_WIDTH // 2 - 100, SCREEN_HEIGHT // 2))
    pygame.display.flip()
    pygame.time.wait(3000)  # Wait for 3 seconds before quitting
    pygame.quit()

# Spawn initial enemies
spawn_enemies()

# Main game loop
running = True
while running:
    screen.fill(WHITE)
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    keys = pygame.key.get_pressed()

    # Movement
    if keys[pygame.K_LEFT]:
        player_x -= player_speed
    if keys[pygame.K_RIGHT]:
        player_x += player_speed

    # Jumping
    if not is_jumping and keys[pygame.K_SPACE]:
        is_jumping = True
        player_vel_y = -player_jump_height

    if is_jumping:
        # Apply gravity
        player_y += player_vel_y
        player_vel_y += gravity

        # Check if the player has landed
        if player_y >= ground_level:
            player_y = ground_level
            is_jumping = False

    # Sword swinging
    if keys[pygame.K_s]:
        sword_swinging = True
    else:
        sword_swinging = False

    # Move enemies and check for collisions
    move_enemies()
    check_collisions()

    # Draw everything
    draw_player()
    draw_sword() if sword_swinging else None
    draw_enemies()
    draw_health()

    # Check win/lose condition
    if player_health <= 0:
        game_over()
        running = False
    elif not enemy_list:  # All enemies defeated
        game_over()
        running = False

    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()

You 
i want to make this into a game
