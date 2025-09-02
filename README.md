import pygame
import random

# Inicializar o Pygame
pygame.init()

# Definir as cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERDE = (0, 255, 0)
CINZA = (169, 169, 169)
AZUL = (0, 0, 255)
VERMELHO = (255, 0, 0)

# Tamanho da tela
LARGURA_TELA = 800
ALTURA_TELA = 600
tela = pygame.display.set_mode((LARGURA_TELA, ALTURA_TELA))
pygame.display.set_caption("Jogo de Corrida")

# Velocidade do jogo
FPS = 60
clock = pygame.time.Clock()

# Definir o carro do jogador
carro_largura = 50
carro_altura = 100

# Função para desenhar o carro do jogador
def desenha_carro(x, y):
    pygame.draw.rect(tela, AZUL, [x, y, carro_largura, carro_altura])

# Função para exibir a pontuação
def exibir_pontuacao(pontos):
    fonte = pygame.font.SysFont("Arial", 30)
    texto = fonte.render(f"Pontos: {pontos}", True, BRANCO)
    tela.blit(texto, [10, 10])

# Função principal do jogo
def jogo():
    # Posições iniciais do carro
    x = LARGURA_TELA // 2 - carro_largura // 2
    y = ALTURA_TELA - carro_altura - 10
    velocidade_x = 0
    velocidade_carro = 5
    obstaculos = []
    pontos = 0

    # Loop principal do jogo
    jogo_ativo = True
    while jogo_ativo:
        for evento in pygame.event.get():
            if evento.type == pygame.QUIT:
                jogo_ativo = False
            if evento.type == pygame.KEYDOWN:
                if evento.key == pygame.K_LEFT:
                    velocidade_x = -velocidade_carro
                if evento.key == pygame.K_RIGHT:
                    velocidade_x = velocidade_carro
            if evento.type == pygame.KEYUP:
                if evento.key == pygame.K_LEFT or evento.key == pygame.K_RIGHT:
                    velocidade_x = 0

        # Atualizar posição do carro
        x += velocidade_x

        # Impedir que o carro saia da tela
        if x < 0:
            x = 0
        if x > LARGURA_TELA - carro_largura:
            x = LARGURA_TELA - carro_largura

        # Gerar obstáculos aleatórios
        if random.randint(1, 30) == 1:
            obstaculo_x = random.randint(0, LARGURA_TELA - 50)
            obstaculo_y = -50
            obstaculos.append([obstaculo_x, obstaculo_y])

        # Mover e desenhar obstáculos
        for obstaculo in obstaculos[:]:
            obstaculo[1] += 5
            pygame.draw.rect(tela, VERMELHO, [obstaculo[0], obstaculo[1], 50, 50])

            # Verificar colisão
            if obstaculo[1] > ALTURA_TELA:
                obstaculos.remove(obstaculo)
                pontos += 1
            elif obstaculo[1] + 50 > y and obstaculo[0] + 50 > x and obstaculo[0] < x + carro_largura:
                # Colisão com o carro
                jogo_ativo = False

        # Preencher o fundo da tela
        tela.fill(CINZA)

        # Desenhar o carro
        desenha_carro(x, y)

        # Exibir a pontuação
        exibir_pontuacao(pontos)

        # Atualizar a tela
        pygame.display.update()

        # Controlar a taxa de quadros
        clock.tick(FPS)

    # Exibir mensagem de game over
    fonte = pygame.font.SysFont("Arial", 50)
    texto = fonte.render(f"Fim de Jogo! Pontos: {pontos}", True, VERMELHO)
    tela.fill(PRETO)
    tela.blit(texto, [LARGURA_TELA // 2 - texto.get_width() // 2, ALTURA_TELA // 2 - texto.get_height() // 2])
    pygame.display.update()
    pygame.time.delay(3000)  # Esperar 3 segundos antes de fechar

# Rodar o jogo
jogo()

# Fechar o Pygame
pygame.quit()
