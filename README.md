import pygame
import random
import sys

# Inicializa o Pygame
pygame.init()

# Configurações da tela
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Garoto da Academia")

# Cores
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)

# FPS
clock = pygame.time.Clock()
FPS = 60

# Personagem
player_width, player_height = 50, 50
player_x, player_y = WIDTH // 2, HEIGHT - 70
player_speed = 5

# Halteres (obstáculos)
dumbbells = []
dumbbell_width, dumbbell_height = 30, 30
dumbbell_speed = 5

# Pesos (objetivo)
weights = []
weight_width, weight_height = 30, 30
weight_speed = 3

# Pontuação e Energia
points = 0
energy = 100

# Fonte
font = pygame.font.Font(None, 36)

# Função para desenhar texto
def draw_text(text, color, x, y):
    render = font.render(text, True, color)
    screen.blit(render, (x, y))

# Loop principal
running = True
while running:
    screen.fill(WHITE)
    
    # Eventos
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    
    # Controles
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_x > 0:
        player_x -= player_speed
    if keys[pygame.K_RIGHT] and player_x < WIDTH - player_width:
        player_x += player_speed
    
    # Adicionar obstáculos e pesos
    if random.randint(1, 60) == 1:
        dumbbells.append([random.randint(0, WIDTH - dumbbell_width), 0])
    if random.randint(1, 80) == 1:
        weights.append([random.randint(0, WIDTH - weight_width), 0])
    
    # Atualizar posição dos objetos
    for dumbbell in dumbbells:
        dumbbell[1] += dumbbell_speed
    for weight in weights:
        weight[1] += weight_speed

    # Detectar colisões
    player_rect = pygame.Rect(player_x, player_y, player_width, player_height)
    for dumbbell in dumbbells[:]:
        if player_rect.colliderect(pygame.Rect(*dumbbell, dumbbell_width, dumbbell_height)):
            dumbbells.remove(dumbbell)
            energy -= 10
    for weight in weights[:]:
        if player_rect.colliderect(pygame.Rect(*weight, weight_width, weight_height)):
            weights.remove(weight)
            points += 1

    # Desenhar personagem
    pygame.draw.rect(screen, GREEN, (player_x, player_y, player_width, player_height))

    # Desenhar obstáculos e pesos
    for dumbbell in dumbbells:
        pygame.draw.rect(screen, RED, (*dumbbell, dumbbell_width, dumbbell_height))
    for weight in weights:
        pygame.draw.rect(screen, BLACK, (*weight, weight_width, weight_height))

    # Mostrar pontuação e energia
    draw_text(f"Pontos: {points}", BLACK, 10, 10)
    draw_text(f"Energia: {energy}", BLACK, 10, 40)

    # Condicional de vitória/perda
    if points >= 10:
        draw_text("Você venceu!", GREEN, WIDTH // 2 - 100, HEIGHT // 2)
        pygame.display.flip()
        pygame.time.wait(3000)
        running = False
    if energy <= 0:
        draw_text("Você perdeu!", RED, WIDTH // 2 - 100, HEIGHT // 2)
        pygame.display.flip()
        pygame.time.wait(3000)
        running = False

    # Atualizar tela
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
sys.exit()


