# pongimport pygame
import sys

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
PADDLE_WIDTH, PADDLE_HEIGHT = 10, 100
BALL_SIZE = 20
FPS = 60

# Set up the screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Pong")

# Paddle class
class Paddle:
    def __init__(self, x):
        self.rect = pygame.Rect(x, HEIGHT // 2 - PADDLE_HEIGHT // 2, PADDLE_WIDTH, PADDLE_HEIGHT)
        self.speed = 7

    def move(self, up=True):
        if up and self.rect.top > 0:
            self.rect.y -= self.speed
        elif not up and self.rect.bottom < HEIGHT:
            self.rect.y += self.speed

    def draw(self):
        pygame.draw.rect(screen, WHITE, self.rect)

# Ball class
class Ball:
    def __init__(self):
        self.rect = pygame.Rect(WIDTH // 2 - BALL_SIZE // 2, HEIGHT // 2 - BALL_SIZE // 2, BALL_SIZE, BALL_SIZE)
        self.speed_x = 5
        self.speed_y = 5

    def move(self):
        self.rect.x += self.speed_x
        self.rect.y += self.speed_y

        if self.rect.top <= 0 or self.rect.bottom >= HEIGHT:
            self.speed_y *= -1

    def draw(self):
        pygame.draw.ellipse(screen, WHITE, self.rect)

    def reset(self):
        self.rect.center = (WIDTH // 2, HEIGHT // 2)
        self.speed_x *= -1

# Initialize paddles and ball
left_paddle = Paddle(20)
right_paddle = Paddle(WIDTH - 30)
ball = Ball()

# Game loop
clock = pygame.time.Clock()
running = True

while running:
    clock.tick(FPS)
    screen.fill(BLACK)

    # Input handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Key presses
    keys = pygame.key.get_pressed()
    if keys[pygame.K_w]:
        left_paddle.move(up=True)
    if keys[pygame.K_s]:
        left_paddle.move(up=False)
    if keys[pygame.K_UP]:
        right_paddle.move(up=True)
    if keys[pygame.K_DOWN]:
        right_paddle.move(up=False)

    # Move ball
    ball.move()

    # Collision
    if ball.rect.colliderect(left_paddle.rect) or ball.rect.colliderect(right_paddle.rect):
        ball.speed_x *= -1

    if ball.rect.left <= 0 or ball.rect.right >= WIDTH:
        ball.reset()

    # Draw everything
    left_paddle.draw()
    right_paddle.draw()
    ball.draw()

    pygame.display.flip()

# Clean up
pygame.quit()
sys.exit()
