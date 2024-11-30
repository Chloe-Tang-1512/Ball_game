I made this simple game on Python.
To move the paddle, use the left and right arrows.

import tkinter as tk
import random

class CatchTheBallGame:
    def __init__(self, root):
        self.root = root
        self.root.title("Catch the Ball")
        self.canvas = tk.Canvas(root, width=400, height=600, bg="lightblue")
        self.canvas.pack()

        # Initialize paddle
        self.paddle = self.canvas.create_rectangle(150, 550, 250, 570, fill="blue")
        self.paddle_speed = 20

        # Initialize ball
        self.ball = self.canvas.create_oval(190, 10, 210, 30, fill="red")
        self.ball_speed_y = 5
        self.ball_speed_x = random.choice([-3, 3])

        # Score and lives
        self.score = 0
        self.lives = 3
        self.score_text = self.canvas.create_text(10, 10, anchor="nw", font=("Arial", 16), text=f"Score: {self.score}")
        self.lives_text = self.canvas.create_text(300, 10, anchor="nw", font=("Arial", 16), text=f"Lives: {self.lives}")

        # Key bindings
        self.root.bind("<Left>", self.move_left)
        self.root.bind("<Right>", self.move_right)

        # Game loop
        self.update_game()

    def move_left(self, event):
        if self.canvas.coords(self.paddle)[0] > 0:
            self.canvas.move(self.paddle, -self.paddle_speed, 0)

    def move_right(self, event):
        if self.canvas.coords(self.paddle)[2] < 400:
            self.canvas.move(self.paddle, self.paddle_speed, 0)

    def update_game(self):
        self.move_ball()
        self.check_collision()
        self.canvas.itemconfig(self.score_text, text=f"Score: {self.score}")
        self.canvas.itemconfig(self.lives_text, text=f"Lives: {self.lives}")
        if self.lives > 0:
            self.root.after(20, self.update_game)
        else:
            self.canvas.create_text(200, 300, text="Game Over", font=("Arial", 24), fill="red")

    def move_ball(self):
        self.canvas.move(self.ball, self.ball_speed_x, self.ball_speed_y)
        ball_coords = self.canvas.coords(self.ball)

        # Ball bouncing off walls
        if ball_coords[0] <= 0 or ball_coords[2] >= 400:
            self.ball_speed_x = -self.ball_speed_x

        # Ball hitting the bottom
        if ball_coords[3] >= 600:
            self.reset_ball()
            self.lives -= 1

        # Ball hitting the top
        if ball_coords[1] <= 0:
            self.ball_speed_y = -self.ball_speed_y

    def reset_ball(self):
        self.canvas.coords(self.ball, 190, 10, 210, 30)
        self.ball_speed_y = 5
        self.ball_speed_x = random.choice([-3, 3])

    def check_collision(self):
        ball_coords = self.canvas.coords(self.ball)
        paddle_coords = self.canvas.coords(self.paddle)

        # Check if ball hits the paddle
        if (
            ball_coords[2] >= paddle_coords[0]
            and ball_coords[0] <= paddle_coords[2]
            and ball_coords[3] >= paddle_coords[1]
            and ball_coords[1] <= paddle_coords[3]
        ):
            self.ball_speed_y = -self.ball_speed_y
            self.score += 1

# Run the game
if __name__ == "__main__":
    root = tk.Tk()
    game = CatchTheBallGame(root)
    root.mainloop()
