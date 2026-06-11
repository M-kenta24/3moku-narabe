# 3moku-narabe
import pygame
import sys

# 初期化
pygame.init()

# 画面設定
WIDTH, HEIGHT = 600, 700
BOARD_SIZE = 600
CELL_SIZE = BOARD_SIZE // 3

screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("三目並べ")

# 色
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
BLUE = (50, 100, 255)
RED = (255, 80, 80)
GREEN = (0, 180, 0)

# フォント
font = pygame.font.SysFont(None, 60)
small_font = pygame.font.SysFont(None, 40)

# ゲーム状態
board = [["" for _ in range(3)] for _ in range(3)]
current_player = "X"
winner = None
game_over = False

def draw_board():
    screen.fill(WHITE)

    # グリッド
    for i in range(1, 3):
        pygame.draw.line(
            screen, BLACK,
            (i * CELL_SIZE, 0),
            (i * CELL_SIZE, BOARD_SIZE),
            5
        )
        pygame.draw.line(
            screen, BLACK,
            (0, i * CELL_SIZE),
            (BOARD_SIZE, i * CELL_SIZE),
            5
        )

    # X/O描画
    for row in range(3):
        for col in range(3):
            x = col * CELL_SIZE
            y = row * CELL_SIZE

            if board[row][col] == "X":
                pygame.draw.line(
                    screen, RED,
                    (x + 30, y + 30),
                    (x + CELL_SIZE - 30, y + CELL_SIZE - 30),
                    8
                )
                pygame.draw.line(
                    screen, RED,
                    (x + CELL_SIZE - 30, y + 30),
                    (x + 30, y + CELL_SIZE - 30),
                    8
                )

            elif board[row][col] == "O":
                pygame.draw.circle(
                    screen, BLUE,
                    (x + CELL_SIZE // 2, y + CELL_SIZE // 2),
                    CELL_SIZE // 2 - 30,
                    8
                )

    # ステータス表示
    pygame.draw.rect(screen, (240, 240, 240), (0, 600, 600, 100))

    if game_over:
        if winner:
            text = font.render(f"{winner} wins!", True, GREEN)
        else:
            text = font.render("Draw!", True, BLACK)
    else:
        text = font.render(f"Turn: {current_player}", True, BLACK)

    screen.blit(text, (20, 625))

    reset_text = small_font.render(
        "Press R to Reset",
        True,
        BLACK
    )
    screen.blit(reset_text, (330, 640))

def check_winner():
    # 行
    for row in range(3):
        if (
            board[row][0] != "" and
            board[row][0] == board[row][1] == board[row][2]
        ):
            return board[row][0]

    # 列
    for col in range(3):
        if (
            board[0][col] != "" and
            board[0][col] == board[1][col] == board[2][col]
        ):
            return board[0][col]

    # 斜め
    if (
        board[0][0] != "" and
        board[0][0] == board[1][1] == board[2][2]
    ):
        return board[0][0]

    if (
        board[0][2] != "" and
        board[0][2] == board[1][1] == board[2][0]
    ):
        return board[0][2]

    return None

def board_full():
    for row in board:
        for cell in row:
            if cell == "":
                return False
    return True

def reset_game():
    global board, current_player, winner, game_over

    board = [["" for _ in range(3)] for _ in range(3)]
    current_player = "X"
    winner = None
    game_over = False

clock = pygame.time.Clock()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_r:
                reset_game()

        if (
            event.type == pygame.MOUSEBUTTONDOWN
            and not game_over
        ):
            mx, my = pygame.mouse.get_pos()

            if my < BOARD_SIZE:
                row = my // CELL_SIZE
                col = mx // CELL_SIZE

                if board[row][col] == "":
                    board[row][col] = current_player

                    winner = check_winner()

                    if winner:
                        game_over = True

                    elif board_full():
                        game_over = True

                    else:
                        current_player = (
                            "O" if current_player == "X" else "X"
                        )

    draw_board()
    pygame.display.flip()
    clock.tick(60)
