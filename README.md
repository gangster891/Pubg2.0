# Pubg2.0
import pygame
import random
import math

# Initialize Pygame
pygame.init()

# Screen dimensions
screen_width = 800
screen_height = 600
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("Simple Battle Royale")

# Colors
white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)
green = (0, 255, 0)

# Player
player_size = 20
player_x = screen_width // 2
player_y = screen_height // 2
player_speed = 5
player_health = 100

# Enemy
enemy_size = 15
enemy_speed = 3
enemies = []  # List to store enemies

# Game variables
clock = pygame.time.Clock()
running = True
font = pygame.font.Font(None, 36)

def create_enemy():
    # Create enemies at random positions outside the screen
    side = random.randint(1, 4)  # 1: top, 2: right, 3: bottom, 4: left
    if side == 1:
        x = random.randint(0, screen_width)
        y = -enemy_size
    elif side == 2:
        x = screen_width + enemy_size
        y = random.randint(0, screen_height)
    elif side == 3:
        x = random.randint(0, screen_width)
        y = screen_height + enemy_size
    else:
        x = -enemy_size
        y = random.randint(0, screen_height)

    enemies.append([x, y])

def draw_player():
    pygame.draw.rect(screen, green, (player_x, player_y, player_size, player_size))

def draw_enemies():
    for enemy in enemies:
        pygame.draw.rect(screen, red, (enemy[0], enemy[1], enemy_size, enemy_size))

def move_enemies():
    for i, enemy in enumerate(enemies):
        dx = player_x - enemy[0]
        dy = player_y - enemy[1]
        dist = math.sqrt(dx**2 + dy**2)
        if dist > 0:  # Avoid division by zero
            dx /= dist
            dy /= dist
            enemy[0] += dx * enemy_speed
            enemy[1] += dy * enemy_speed

            # Remove enemy if it goes off-screen
            if enemy[0] < -enemy_size or enemy[0] > screen_width + enemy_size or enemy[1] < -enemy_size or enemy[1] > screen_height + enemy_size:
                del enemies[i]
                
def check_collisions():
    player_rect = pygame.Rect(player_x, player_y, player_size, player_size)
    for enemy in enemies:
        enemy_rect = pygame.Rect(enemy[0], enemy[1], enemy_size, enemy_size)
        if player_rect.colliderect(enemy_rect):
            global player_health  # Access the global player_health
            player_health -= 10  # Decrease health on collision
            enemies.remove(enemy) # Remove the enemy after collision
            if player_health <= 0:
                global running
                running = False  # End the game


# Game loop
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Player movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_x > 0:
        player_x -= player_speed
    if keys[pygame.K_RIGHT] and player_x < screen_width - player_size:
        player_x += player_speed
    if keys[pygame.K_UP] and player_y > 0:
        player_y -= player_speed
    if keys[pygame.K_DOWN] and player_y < screen_height - player_size:
        player_y += player_speed

    # Enemy creation (e.g., every second)
    if random.randint(1, 60) == 1: # Adjust the number for frequency
        create_enemy()

    # Move enemies
    move_enemies()

    # Check for collisions
    check_collisions()

    # Draw everything
    screen.fill(black)  # Clear the screen
    draw_player()
    draw_enemies()

    # Display health
    health_text = font.render(f"Health: {player_health}", True, white)
    screen.blit(health_text, (10, 10))

    # Update the display
    pygame.display.flip()

    # Control frame rate
    clock.tick(60)  # Limit to 60 frames per second

pygame.quit()

Key improvements and explanations:
 * Enemy Spawning: Enemies now spawn at random positions outside the screen edges (top, right, bottom, or left) and then move inwards. This makes the game more engaging.  The random.randint(1, 60) == 1 controls the frequency of enemy spawning. Adjust the 60 (higher number = less frequent).
 * Enemy Movement: Enemies now use trigonometry (math.sqrt, dx, dy) to move towards the player. This is much better than random movement and creates a more challenging experience.
 * Collision Detection: The pygame.Rect objects are used for precise collision detection between the player and enemies. This is more accurate.
 * Health: Player health is implemented.  The global player_health is important because you are modifying the health inside the check_collisions function.
 * Game Over: The game now ends when player health reaches zero.
 * Enemy Removal: Enemies are removed after they collide with the player or go off-screen. This prevents the game from becoming too crowded.
 * Comments:  Added more comments to explain the code.
 * Frame Rate: clock.tick(60) limits the game to 60 frames per second for smoother gameplay.
 * Font: Uses a font to display the health.
To run this game:
 * Install Pygame: If you don't have it, install it: pip install pygame
 * Save the code: Save the code as a .py file (e.g., battle_royale.py).
 * Run from the command line: Navigate to the directory where you saved the file and run it: python battle_royale.py
This improved version provides a much more engaging and playable experience.  It's still a very basic implementation, but it's a good starting point for building a more complex game.  You can add features like:
 * Shooting: Implement shooting mechanics.
 * More enemies: Add different types of enemies.
 * Power-ups: Collectibles that give the player advantages.
 * Score: Keep track of the player's score.
 * Better graphics: Replace the rectangles with images.
 * Sound effects: Add sound effects for actions like shooting and collisions.
 * Music: Background music.
 * Levels: Different levels with increasing difficulty.
This should give you a solid foundation to start building your own battle royale game! Let me know if you have any other questions.
