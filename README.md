import pygame
import sys

# Constants
WINDOW_SIZE = (800, 400)
TITLE_GAME = "Pong Game"
WHITE = (255, 255, 255)
BG_COLOR = (2, 102, 11)
PADDLE_SPEED = 5
P_WIDTH = 10
P_HEIGHT = 60
BALL_SIZE = 20, 20
BALL_SPEED = 6

# Init
pygame.init()
gd = pygame.display.set_mode(WINDOW_SIZE)
pygame.display.set_caption(TITLE_GAME)

# Objects
opponent = pygame.Rect(20, 400 // 2 - P_HEIGHT // 2, P_WIDTH, P_HEIGHT)
player = pygame.Rect(800 - 20 - P_WIDTH, 400 // 2 - P_HEIGHT // 2, P_WIDTH, P_HEIGHT)
FONT = pygame.font.Font(None, 36)
clock = pygame.time.Clock()
ball = pygame.Rect(800 // 2 - BALL_SIZE[0] // 2, 400 // 2 - BALL_SIZE[1] // 2, *BALL_SIZE)
ball_xd = BALL_SPEED
ball_yd = BALL_SPEED

player_score = 0
opponent_score = 0

# Function to show score
def show_score(surface, _str, color, which):
    str_obj = FONT.render(_str, True, color)
    position = list(str_obj.get_size())
    if which:
        position[0] = (800 // 2) - position[0] - 15
    else:
        position[0] = (800 // 2) + 15
    surface.blit(str_obj, position)

# Main game loop
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    keys = pygame.key.get_pressed()
    if keys[pygame.K_UP] and player.top >= 0:
        player.y -= PADDLE_SPEED
    if keys[pygame.K_DOWN] and player.bottom <= 400:
        player.y += PADDLE_SPEED

    # Ball movement
    if ball.bottom >= 400 or ball.top <= 0:
        ball_yd *= -1

    # Check for collisions with paddles
    if ball.colliderect(player) and ball_xd > 0:
        ball_xd *= -1
    elif ball.colliderect(opponent) and ball_xd < 0:
        ball_xd *= -1

    ball.x += ball_xd
    ball.y += ball_yd

    # Check for scoring
    if ball.left > 800:
        opponent_score += 1
        ball.x, ball.y = 800 // 2 - BALL_SIZE[0] // 2, 400 // 2 - BALL_SIZE[1] // 2
        ball_xd = -BALL_SPEED

    elif ball.right < 0:
        player_score += 1
        ball.x, ball.y = 800 // 2 - BALL_SIZE[0] // 2, 400 // 2 - BALL_SIZE[1] // 2
        ball_xd = BALL_SPEED

    if opponent.center[1] < ball.center[1] and opponent.bottom < 400:
        opponent.y += PADDLE_SPEED
    if opponent.center[1] > ball.center[1] and opponent.top > 0:
        opponent.y -= PADDLE_SPEED

    # Drawing
    def drawing():
        gd.fill(BG_COLOR)
        show_score(gd, str(opponent_score), WHITE, True)
        show_score(gd, str(player_score), WHITE, False)
        pygame.draw.ellipse(gd, WHITE, ball)
        pygame.draw.rect(gd, WHITE, player)
        pygame.draw.circle(gd, WHITE, (400, 200), 100, 4)
        pygame.draw.rect(gd, WHITE, opponent)
        pygame.draw.line(gd, WHITE, (800 // 2, 0), (800 // 2, 400))
        pygame.draw.circle(gd, WHITE, (400, 200), 20, 3)

    drawing()
    pygame.display.update()
    clock.tick(70)
