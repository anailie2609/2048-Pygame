# 2048-Pygame
# Proiect Python - Joc 2048

import pygame
import random
pygame.init()

# setari initiale
HEIGHT = 500
WIDTH = 400
screen = pygame.display.set_mode([WIDTH,HEIGHT])
pygame.display.set_caption('2048')
timer = pygame.time.Clock()
fps = 60 #majoritatea jocurilor au viteza de 60fps
font = pygame.font.Font('freesansbold.ttf',24) #marimea fontului este 24 si aspectul este dat de freesansbold.ttf


# 2048 librarie de culori intr un dictionar
colors = {0: (238, 210,238),
          2: (242, 210, 242),
          4: (210, 202, 236),
          8: (2, 204, 255),
          16: (201, 160, 220),
          32: (246, 124, 95),
          64: (223, 112, 250),
          128: (242, 93, 247),
          256: (237, 90, 245),
          512: (182, 100, 210),
          1024: (161, 6, 132),
          2048: (108, 2, 119),
          'light text': (249, 246, 242),
          'dark text': (119, 110, 101),
          'other': (0, 0, 0),
          'bg': (254, 231, 240)}


# variabilele jocului intr o lista de liste
board_values = [[0 for _ in range(4)] for _ in range(4)] #creearea unui grid de 4x4
game_over = False
spawn_new = True
init_count = 0
direction = ''
score = 0


# reinceperea jocului dupa ce am pierdut
def draw_over():
    pygame.draw.rect(screen, 'white', [50,50,300,100], 0, 10)
    game_over_text1 = font.render('Game Over!', True, 'pink')
    game_over_text2 = font.render('Press Enter to restart', True, 'pink')
    screen.blit(game_over_text1, (130, 65))
    screen.blit(game_over_text2, (70, 105))


#plasarea in functie de directie
def take_turn(direc, board):
    global score
    merged = [[False for _ in range(4)] for _ in range(4)]
    if direc == 'UP':
        for i in range(4):
            for j in range(4):
                shift = 0
                if i > 0 :
                    for q in range(i):
                        if board[q][j] == 0:
                            shift += 1
                    if shift > 0:
                        board[i-shift][j] = board[i][j]
                        board[i][j] = 0
                    if board[i - shift - 1][j] == board[i - shift][j] and not merged[i - shift][j] \
                            and not merged[i - shift - 1][j]:
                        board[i - shift - 1][j] *= 2
                        score+=board[i - shift - 1][j]
                        board[i - shift][j] = 0
                        merged[i - shift - 1][j] = True

elif direc == 'DOWN':
        for i in range(3):
            for j in range(4):
                shift = 0
                for q in range(i + 1):
                    if board[3 - q][j] == 0:
                        shift += 1
                if shift > 0:
                    board[2 - i + shift][j] = board[2 - i][j]
                    board[2 - i][j] = 0
                if 3 - i + shift <= 3:
                    if board[2 - i + shift][j] == board[3 - i + shift][j] and not merged[3 - i + shift][j] \
                            and not merged[2 - i + shift][j]:
                        board[3 - i + shift][j] *= 2
                        score+=board[3 - i + shift][j]
                        board[2 - i + shift][j] = 0
                        merged[3 - i + shift][j] = True

elif direc == 'LEFT':
        for i in range(4):
            for j in range(4):
                shift = 0
                for q in range(j):
                    if board[i][q] == 0:
                        shift += 1
                if shift > 0:
                    board[i][j - shift] = board[i][j]
                    board[i][j] = 0
                if board[i][j - shift] == board[i][j - shift - 1] and not merged[i][j - shift - 1] \
                        and not merged[i][j - shift]:
                    board[i][j - shift - 1] *= 2
                    score+=board[i][j - shift - 1]
                    board[i][j - shift] = 0
                    merged[i][j - shift - 1] = True

elif direc == 'RIGHT':
        for i in range(4):
            for j in range(4):
                shift = 0
                for q in range(j):
                    if board[i][3 - q] == 0:
                        shift += 1
                if shift > 0:
                    board[i][3 - j + shift] = board[i][3 - j]
                    board[i][3 - j] = 0
                if 4 - j + shift <= 3:
                    if board[i][4 - j + shift] == board[i][3 - j + shift] and not merged[i][4 - j + shift] \
                            and not merged[i][3 - j + shift]:
                        board[i][4 - j + shift] *= 2
                        score+=board[i][4 - j + shift]
                        board[i][3 - j + shift] = 0
                        merged[i][4 - j + shift] = True
    return board

  
# aparitia pieselor la start aleatoriu
# putem avea 2 sau 4 cu o sansa de 1/10 la aparitia lui 4

def new_pieces(board):
    count = 0
    full = False
    while any(0 in row for row in board) and count < 1:
        row = random.randint(0,3)
        col= random.randint(0,3)
        if board[row][col] == 0 :
            count += 1
            if random.randint(1,10) == 10:
                board[row][col] = 4
            else :
                board[row][col] = 2
    if count < 1:
        full = True
    return board, full


# fundalul tablei jocului
def draw_board():
    pygame.draw.rect(screen,colors['bg'],[0, 0,400,400], 0,10) #culoarea tablei de joc,dimensiunea tablei
    score_text = font.render(f'Score: {score}' , True, 'blue')
    screen.blit(score_text, (10, 410))
    pass 


# structura patratelelor
def draw_pieces(board):
    for i in range(4):
        for j in range(4):
            value = board[i][j]
            if value > 8 :
                value_color = colors['light text']
            else:
                value_color = colors['dark text'] 
                 #alegerea culorii textului pentru fiecare patrat (mai inchis pentru patratele deschise si invers ) si daca ajungi la 2048 se face fundalul negru
            if value <= 2048 :
                color = colors[value]
            else: 
                color = colors['other']        
            pygame.draw.rect(screen,color,[j*95+20, i*95+20, 75,75],0,5)    #coordonatele patratelor si dimensiunile patratelor(75)si spatiul intre ele de 20 
            if value > 0:
                value_len = len(str(value))
                font = pygame.font.Font('freesansbold.ttf',48-(5*value_len)) #fontul  pentru fiecare patrat (se micsoreaza cu 5 cand creste valoare piesei)
                value_text = font.render(str(value), True, value_color)
                text_rect = value_text.get_rect(center=(j*95+57, i*95+57)) #centratrea textului din fiecare patrat intr un alt patrat
                screen.blit(value_text, text_rect) #plasarea imiaginii cu patratul ce contine cifra(2,4,...)
                pygame.draw.rect(screen,'white', [j*95+20, i*95+20, 75,75], 2, 5)
                
    
# structura principala a jocului
# am creat afisarea display-ului alb(fundalul) si inchiderea jocului cand se apasa butonul X de iesire
run = True
while run:
    timer.tick(fps)
    screen.fill('white')
    draw_board()
    draw_pieces(board_values)
    if spawn_new or init_count<2:    
        #pentru a insera prima data 2 patratele
        board_values, game_over = new_pieces(board_values)
        spawn_new = False
        init_count += 1
    if direction != '':
        board_values = take_turn(direction, board_values)
        direction = ''
        spawn_new = True
    if game_over:
        draw_over()

# directia de mutarea patratelelor
for event in pygame.event.get():
        if event.type == pygame.QUIT:
            run = False
        if event.type == pygame.KEYUP:
            if event.key == pygame.K_UP :
                 direction = 'UP'
            elif event.key == pygame.K_DOWN :
                 direction = 'DOWN'
            elif event.key == pygame.K_LEFT :
                 direction = 'LEFT'
            elif event.key == pygame.K_RIGHT :
                 direction = 'RIGHT'
          if game_over:
                if event.key == pygame.K_RETURN:
                    board_values = [[0 for _ in range(4)] for _ in range(4)]
                    spawn_new = True
                    init_count = 0
                    score = 0
                    direction = ''
                    game_over = False
    pygame.display.flip()
pygame.quit()
