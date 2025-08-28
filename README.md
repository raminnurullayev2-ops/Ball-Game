# Ball-Game

import flet as ft
import flet.canvas as cv

def main(page: ft.Page):
    page.title = "Joystick ilə Top Atma"
    page.bgcolor = ft.colors.BLACK
    page.window_width = 900
    page.window_height = 600
    page.update()

    width, height = 800, 500  # oyun sahəsi
    ball_radius = 30
    ball_x, ball_y = width//2, height//2
    ball_vel_x, ball_vel_y = 0, 0
    friction = 0.95

    goal_width, goal_height = 80, 120
    goal_x, goal_y = width - goal_width - 10, height//2 - goal_height//2

    score = ft.Text("Xal: 0", size=20, color=ft.colors.WHITE)

    # Canvas elementləri
    ball = cv.Circle(ball_x, ball_y, ball_radius,
                     paint=ft.Paint(ft.PaintingStyle.FILL, ft.colors.RED))
    goal = cv.Rect(goal_x, goal_y, goal_width, goal_height,
                   paint=ft.Paint(ft.PaintingStyle.FILL, ft.colors.GREEN))

    canvas = cv.Canvas([goal, ball], width=width, height=height,
                       bgcolor=ft.colors.BLACK)

    def game_tick(e):
        nonlocal ball_x, ball_y, ball_vel_x, ball_vel_y

        # Top hərəkəti
        ball_x += ball_vel_x
        ball_y += ball_vel_y
        ball_vel_x *= friction
        ball_vel_y *= friction

        # Divarlara dəymə
        if ball_x - ball_radius < 0 or ball_x + ball_radius > width:
            ball_vel_x = -ball_vel_x
        if ball_y - ball_radius < 0 or ball_y + ball_radius > height:
            ball_vel_y = -ball_vel_y

        # Qalaya düşdü?
        if goal_x < ball_x < goal_x + goal_width and goal_y < ball_y < goal_y + goal_height:
            val = int(score.value.split(": ")[1]) + 1
            score.value = f"Xal: {val}"
            ball_x, ball_y = width//2, height//2
            ball_vel_x = ball_vel_y = 0

        # Canvas obyektlərini yenilə
        ball.x, ball.y = ball_x, ball_y
        canvas.update()
        score.update()

    def kick(e):
        """Topa istiqamət ver (Sol/Sağ düyməsi)."""
        nonlocal ball_vel_x, ball_vel_y
        if e.control.text == "Sağ":
            ball_vel_x = 12
            ball_vel_y = -6
        else:
            ball_vel_x = -12
            ball_vel_y = -6

    # İdarə düymələri
    controls = ft.Row(
        [
            ft.ElevatedButton("Sol", on_click=kick),
            ft.ElevatedButton("Sağ", on_click=kick),
        ],
        alignment=ft.MainAxisAlignment.CENTER,
    )

    page.add(score, canvas, controls)

    # Timer – oyunu yeniləmək üçün
    page.timer_interval_ms = 30
    page.on_timer = game_tick
    page.timer_start()

# 🚀 Burada view=ft.AppView.FLET_APP verdim ki, ayrıca pəncərə açılsın
ft.app(target=main, view=ft.AppView.FLET_APP)
