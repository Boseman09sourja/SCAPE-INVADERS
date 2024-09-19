# SCAPE-INVADERS

# Import the modules of pygame
import pygame
import random
import math

# Initialize the pygame
pygame.init()

# Creating the background
background = pygame.image.load("image copy.png")

# Create a screen for pygame
screen = pygame.display.set_mode((800, 600))

# Title and Icon
pygame.display.set_caption("SPACE INVADERS")
icon = pygame.image.load("image.png")
pygame.display.set_icon(icon)

# Player
playerImg = pygame.image.load("arcade-game.png")
playerX = 370
playerY = 485
playerX_change = 0

# Enemy
enemyImg = []
enemyX = []
enemyY = []
enemyX_change = []
enemyY_change = []
num_of_enemies = 6

for i in range(num_of_enemies):
    enemyImg.append(pygame.image.load("alien.png"))
    enemyX.append(random.randint(0, 735))
    enemyY.append(random.randint(50, 100))
    enemyX_change.append(0.3)
    enemyY_change.append(40)

# Bullet
# Ready state - you can't see the bullet on the screen
# Fire state - bullet is moving 
bulletImg = pygame.image.load("bullet (4).png")
bulletX = 0
bulletY = 485
bulletX_change = 0
bulletY_change = 0.8
bullet_state = "ready"  

score = 0 # For counting the score
font = pygame.font.Font('freesansbold.ttf', 32)

textX = 10
textY = 10

# Game over text
over = pygame.font.Font("freesansbold.ttf", 64)

def show_score(x, y):
    score_value = font.render("Score : "+ str(score), True, (255, 255, 255))
    screen.blit(score_value, (x,y))

def game_over_text():
    over_text = over.render("GAME OVER", True, (255, 255, 255))
    screen.blit(over_text, (200, 250))


def player(x, y):
    screen.blit(playerImg, (x, y))  # Drawing the image on the game window

def enemy(x, y, i):
    screen.blit(enemyImg[i], (x, y))

def fire_bullet(x, y):
    global bullet_state
    bullet_state = "fire"
    screen.blit(bulletImg, (x+17, y+10))

def isCollision(enemyX, enemyY, bulletX, bulletY):
    distance = math.sqrt(math.pow(enemyX - bulletX, 2) + (math.pow(enemyY - bulletY, 2)))
    if distance < 27:
        return True
    else:
        return False

# Create an infinite game loop
running = True
while running:

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        # If keystroke is pressed check whether it is left or right
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                playerX_change = -0.3
            if event.key == pygame.K_RIGHT:
                playerX_change = 0.3
            if event.key == pygame.K_SPACE:
                if bullet_state is "ready":
                    bulletX = playerX # Get the current X coordinate of the spaceship
                    fire_bullet(bulletX, bulletY)

        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                playerX_change = 0  

    # RGB color coding for background
    screen.fill((0, 0, 0))
    screen.blit(background, (0,0))

    playerX += playerX_change

    # Boundary of the spaceship
    if playerX <= 0:
        playerX = 0
    elif playerX >= 736:
        playerX = 736

    # Enemy movement
    for i in range(num_of_enemies):

        # Game Over
        if enemyY[i] > 200:
            for j in range(num_of_enemies):
                enemyY[j] = 2000
            game_over_text()
            break

        enemyX[i] += enemyX_change[i]

    
        # Boundary of the enemy
        if enemyX[i] <= 0:
            enemyX_change[i] = 0.3
            enemyY[i] += enemyY_change[i]
        elif enemyX[i] >= 736:
            enemyX_change[i] = -0.3
            enemyY[i] += enemyY_change[i]

        # Collision 
        collision = isCollision(enemyX[i], enemyY[i], bulletX, bulletY)
        if collision:
            bulletY = 485
            bullet_state = "ready"
            score += 1
            enemyX[i] = random.randint(0, 735)
            enemyY[i] = random.randint(50, 100)

        enemy(enemyX[i], enemyY[i], i)     

    # Bullet movement
    if bulletY <= 0:
        bullet_state = "ready"
        bulletY = 485
    if bullet_state is "fire":
        fire_bullet(bulletX, bulletY)
        bulletY -= bulletY_change

    


    player(playerX, playerY)
    show_score(textX, textY)
    pygame.display.update()

