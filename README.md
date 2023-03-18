# Rolls-Royce-car
Rolls-Royce car game

import pygame
import random

pygame.init()

# set up the screen
screen_width = 800
screen_height = 600
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("Car Game")

# load the images
car_image = pygame.image.load("car.png")
car_width = 60
car_height = 120

# define the car class
class Car:
    def __init__(self, x, y):
        self.image = car_image
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y
        self.speed = 0
    
    def update(self):
        self.rect.x += self.speed
    
    def draw(self):
        screen.blit(self.image, self.rect)

# define the obstacle class
class Obstacle:
    def __init__(self, x, y, speed):
        self.image = pygame.Surface((50, 50))
        self.image.fill((255, 0, 0))
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y
        self.speed = speed
    
    def update(self):
        self.rect.y += self.speed
    
    def draw(self):
        screen.blit(self.image, self.rect)

# create the car and obstacles
car = Car(screen_width/2-car_width/2, screen_height-car_height-50)
obstacles = []

# set up the game loop
clock = pygame.time.Clock()
game_over = False
score = 0

while not game_over:
    # handle events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                car.speed = -5
            elif event.key == pygame.K_RIGHT:
                car.speed = 5
        elif event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT and car.speed < 0:
                car.speed = 0
            elif event.key == pygame.K_RIGHT and car.speed > 0:
                car.speed = 0
    
    # update the car and obstacles
    car.update()
    for obstacle in obstacles:
        obstacle.update()
    
    # add new obstacles
    if random.randint(0, 100) < 10:
        obstacles.append(Obstacle(random.randint(0, screen_width-50), -50, random.randint(1, 5)))
    
    # remove off-screen obstacles
    for obstacle in obstacles:
        if obstacle.rect.y > screen_height:
            obstacles.remove(obstacle)
            score += 1
    
    # check for collisions
    for obstacle in obstacles:
        if car.rect.colliderect(obstacle.rect):
            game_over = True
    
    # draw the screen
    screen.fill((255, 255, 255))
    car.draw()
    for obstacle in obstacles:
        obstacle.draw()
    pygame.display.flip()
    
    # cap the framerate
    clock.tick(60)

# end the game
pygame.quit()
print("Game Over! Score:", score)
