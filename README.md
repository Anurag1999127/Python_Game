# Python_Game
Simple Car Racing Game in Python

import pygame
import random
import os
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
GRAY = (159, 163, 168)
GREEN = (0, 255, 0)
RED = (255, 0, 0)
CAR_COLOR = (135, 38,87)
TEXT_COLOR = (250, 105, 10)
game_path = os.path.dirname(__file__)

pygame.init()


class Car:
    def __init__(self, x=0, y=0, dx=4, dy=0, width=30, height=30, color=RED):
        self.image = ""
        self.x = x
        self.y = y
        self. dx = dx
        self.dy = dy
        self.width = width
        self.height = height
        self.color = color
        self.image = pygame.image.load(os.path.join(game_path,'player.png')).convert()
        self.image.set_colorkey(BLACK)

    def draw_image(self):
        screen.blit(self.image, (self.x, self.y))

    def move_x(self):
        self.x += self.dx

    def move_y(self):
        self.y += self.dy

    def draw_rect(self):
        pygame.draw.rect(screen, self.color, [self.x, self.y, self.width, self.height], 0)

    def check_out_of_screen(self):
        if self.x+self.width > 400 or self.x < 0:
            self.x -= self.dx


def check_collision(player_x, player_y, player_width, player_height, car_x, car_y, car_width, car_height):
    if (player_x+player_width > car_x) and (player_x < car_x+car_width) and (player_y < car_y+car_height) and (player_y+player_height > car_y):
        return True
    else:
        return False


size = (400, 700)
screen = pygame.display.set_mode(size)

pygame.display.set_caption("Ride the Road")

# Loop_Til_clicks_end
done = False

# screen_update
clock = pygame.time.Clock()

# player_car
player = Car(175, 475, 0, 0, 70, 131, RED)

collision = True
# score
score = 0

# fonts
font_40 = pygame.font.SysFont("Arial", 40, True, False)
font_30 = pygame.font.SysFont("Arial", 30, True, False)
text_title = font_40.render("Ride the Road", True, TEXT_COLOR)
text_ins = font_30.render("Click to Play!", True, TEXT_COLOR)


def draw_main_menu():
    screen.blit(text_title, [size[0] / 2 - 106, size[1] / 2 - 100])
    score_text = font_40.render("Score: " + str(score), True, TEXT_COLOR)
    screen.blit(score_text, [size[0] / 2 - 70, size[1] / 2 - 30])
    screen.blit(text_ins, [size[0] / 2 - 85, size[1] / 2 + 40])
    pygame.display.flip()


# block
cars = []
car_count = 2
for i in range(car_count):
    x = random.randrange(0, 340)
    car = Car(x, random.randrange(-150, -50), 0, random.randint(5, 10), 60, 60, CAR_COLOR)
    cars.append(car)

# road
stripes = []
stripe_count = 20
stripe_x = 185
stripe_y = -10
stripe_width = 20
stripe_height = 80
space = 20
for i in range(stripe_count):
    stripes.append([190, stripe_y])
    stripe_y += stripe_height + space

# Main
while not done:
    # main_event
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            done = True

        # Reset_if_new_game
        if collision and event.type == pygame.MOUSEBUTTONDOWN:
            collision = False
            for i in range(car_count):
                cars[i].y = random.randrange(-150, -50)
                cars[i].x = random.randrange(0, 350)
            player.x = 175
            player.dx = 0
            score = 0
            pygame.mouse.set_visible(False)

        if not collision:
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_RIGHT:
                    player.dx = 4
                elif event.key == pygame.K_LEFT:
                    player.dx = -4

            if event.type == pygame.KEYUP:
                if event.key == pygame.K_LEFT:
                    player.dx = 0
                elif event.key == pygame.K_RIGHT:
                    player.dx = 0

    # logic and screen clearing
    screen.fill(GRAY)

    # Drawing 
    if not collision:
        # draw stripes
        for i in range(stripe_count):
            pygame.draw.rect(screen, WHITE, [stripes[i][0], stripes[i][1], stripe_width, stripe_height])
        # Move stripes
        for i in range(stripe_count):
            stripes[i][1] += 3
            if stripes[i][1] > size[1]:
                stripes[i][1] = -40 - stripe_height

        player.draw_image()
        player.move_x()
        player.check_out_of_screen()

        # enemy car limit
        for i in range(car_count):
            cars[i].draw_rect()
            cars[i].y += cars[i].dy
            if cars[i].y > size[1]:
                score += 10
                cars[i].y = random.randrange(-150, -50)
                cars[i].x = random.randrange(0, 340)
                cars[i].dy = random.randint(4, 9)

        # collision of the player with car
        for i in range(car_count):
            if check_collision(player.x, player.y, player.width, player.height, cars[i].x, cars[i].y, cars[i].width, cars[i].height):
                collision = True
                pygame.mouse.set_visible(True)
                break

        # score.
        txt_score = font_30.render("Score: "+str(score), True, WHITE)
        screen.blit(txt_score, [15, 15])

        pygame.display.flip()
    else:
        draw_main_menu()

    # lvl
    clock.tick(60)

# Close the window and quit.
pygame.quit()
