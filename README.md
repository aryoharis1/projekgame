# Flappy Bird Simple
pip install pygame

import pygame
import random

# Initialize pygame
pygame.init()

# Constants
SCREEN_WIDTH = 400
SCREEN_HEIGHT = 600
GROUND_HEIGHT = 100
BIRD_WIDTH = 34
BIRD_HEIGHT = 24
PIPE_WIDTH = 50
PIPE_HEIGHT = 400
PIPE_GAP = 150

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)

# Screen setup
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Flappy Bird")

# Load images
bird_image = pygame.Surface((BIRD_WIDTH, BIRD_HEIGHT))
bird_image.fill(BLACK)
pipe_image = pygame.Surface((PIPE_WIDTH, PIPE_HEIGHT))
pipe_image.fill(GREEN)

# Bird class
class Bird:
    def __init__(self):
        self.x = 50
        self.y = SCREEN_HEIGHT // 2
        self.velocity = 0
        self.gravity = 0.5
        self.flap_power = -10

    def flap(self):
        self.velocity = self.flap_power

    def update(self):
        self.velocity += self.gravity
        self.y += self.velocity

    def draw(self, screen):
        screen.blit(bird_image, (self.x, self.y))

# Pipe class
class Pipe:
    def __init__(self):
        self.x = SCREEN_WIDTH
        self.height = random.randint(50, SCREEN_HEIGHT - GROUND_HEIGHT - PIPE_GAP)
        self.passed = False

    def update(self):
        self.x -= 5

    def draw(self, screen):
        screen.blit(pipe_image, (self.x, self.height - PIPE_HEIGHT))
        screen.blit(pipe_image, (self.x, self.height + PIPE_GAP))

# Game loop
def main():
    clock = pygame.time.Clock()
    bird = Bird()
    pipes = []
    score = 0
    running = True

    while running:
        screen.fill(WHITE)

        # Event handling
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    bird.flap()

        # Update bird
        bird.update()

        # Update pipes
        if len(pipes) == 0 or pipes[-1].x < SCREEN_WIDTH - 200:
            pipes.append(Pipe())
        for pipe in pipes:
            pipe.update()
            if pipe.x + PIPE_WIDTH < bird.x and not pipe.passed:
                score += 1
                pipe.passed = True
            if pipe.x + PIPE_WIDTH < 0:
                pipes.remove(pipe)

        # Collision detection
        for pipe in pipes:
            if bird.x + BIRD_WIDTH > pipe.x and bird.x < pipe.x + PIPE_WIDTH:
                if bird.y < pipe.height or bird.y + BIRD_HEIGHT > pipe.height + PIPE_GAP:
                    running = False

        # Check ground collision
        if bird.y + BIRD_HEIGHT > SCREEN_HEIGHT - GROUND_HEIGHT:
            running = False

        # Draw everything
        bird.draw(screen)
        for pipe in pipes:
            pipe.draw(screen)

        # Display score
        font = pygame.font.SysFont(None, 55)
        text = font.render(f"Score: {score}", True, BLACK)
        screen.blit(text, (10, 10))

        # Refresh screen
        pygame.display.flip()
        clock.tick(30)

    pygame.quit()

if __name__ == "__main__":
    main()
