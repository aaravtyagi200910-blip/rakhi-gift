import tkinter as tk
import random
import math

WIDTH = 900
HEIGHT = 600

root = tk.Tk()
root.title("A Little Question 🎀")
root.geometry(f"{WIDTH}x{HEIGHT}")
root.resizable(False, False)
root.configure(bg="#080812")

canvas = tk.Canvas(
    root,
    width=WIDTH,
    height=HEIGHT,
    bg="#080812",
    highlightthickness=0
)
canvas.pack()


# =========================================================
# BACKGROUND PARTICLES
# =========================================================

particles = []

for _ in range(80):
    x = random.randint(0, WIDTH)
    y = random.randint(0, HEIGHT)
    size = random.choice([1, 2, 2, 3])

    p = canvas.create_oval(
        x, y, x + size, y + size,
        fill="#77728f",
        outline=""
    )

    particles.append([
        p, x, y,
        random.uniform(0.15, 0.6),
        random.uniform(0, math.pi * 2)
    ])


def animate_particles():
    for p in particles:
        p[2] -= p[3]

        if p[2] < -5:
            p[2] = HEIGHT + 5
            p[1] = random.randint(0, WIDTH)

        offset = math.sin(p[2] / 45 + p[4]) * 0.3

        canvas.coords(
            p[0],
            p[1] + offset,
            p[2],
            p[1] + offset + 2,
            p[2] + 2
        )

    root.after(30, animate_particles)


# =========================================================
# MAIN CARD
# =========================================================

canvas.create_rectangle(
    185, 112, 715, 492,
    fill="#17152a",
    outline="#302c4d",
    width=2
)

canvas.create_rectangle(
    195, 122, 705, 482,
    fill="#11111f",
    outline="#24233b"
)


# =========================================================
# QUESTION
# =========================================================

canvas.create_text(
    WIDTH // 2,
    165,
    text="A LITTLE QUESTION",
    fill="#85809d",
    font=("Segoe UI", 10, "bold")
)

canvas.create_text(
    WIDTH // 2,
    210,
    text="🎀  Kya aap Rakhi pe",
    fill="white",
    font=("Segoe UI", 25, "bold")
)

canvas.create_text(
    WIDTH // 2,
    250,
    text="gift lena chahte hain?",
    fill="white",
    font=("Segoe UI", 25, "bold")
)


# =========================================================
# BUTTON CREATION
# =========================================================

def make_button(x, y, text):

    shadow = canvas.create_rectangle(
        x - 75, y - 25,
        x + 75, y + 25,
        fill="#080812",
        outline=""
    )

    button = canvas.create_rectangle(
        x - 72, y - 22,
        x + 72, y + 22,
        fill="#1d1b2d",
        outline="#37334e",
        width=2
    )

    label = canvas.create_text(
        x, y,
        text=text,
        fill="white",
        font=("Segoe UI", 12, "bold")
    )

    return button, label


no_button, no_label = make_button(350, 350, "NO  😭")
yes_button, yes_label = make_button(550, 350, "YES  😎")


# =========================================================
# YES RUN AWAY
# =========================================================

yes_moves = 0

def move_yes(event=None):

    global yes_moves

    yes_moves += 1

    x = random.randint(250, 650)
    y = random.randint(300, 445)

    canvas.coords(
        yes_button,
        x - 72, y - 22,
        x + 72, y + 22
    )

    canvas.coords(
        yes_label,
        x, y
    )

    messages = [
        "NOPE 😂",
        "TOO SLOW 🏃",
        "NAHH 😭",
        "CATCH ME 💨",
        "ALMOST 😂",
        "NO CHANCE 😈"
    ]

    canvas.itemconfig(
        yes_label,
        text=messages[min(yes_moves - 1, len(messages) - 1)]
    )


canvas.tag_bind(yes_button, "<Enter>", move_yes)
canvas.tag_bind(yes_label, "<Enter>", move_yes)


# =========================================================
# NO CLICK
# =========================================================

def no_clicked(event=None):

    # Disable clicking again
    canvas.unbind("<Button-1>")

    transition()


canvas.tag_bind(no_button, "<Button-1>", no_clicked)
canvas.tag_bind(no_label, "<Button-1>", no_clicked)


# =========================================================
# TRANSITION FLASH
# =========================================================

def transition():

    canvas.delete("all")

    canvas.configure(bg="#080812")

    text = canvas.create_text(
        WIDTH // 2,
        HEIGHT // 2,
        text="MISSION ACCOMPLISHED",
        fill="white",
        font=("Segoe UI", 25, "bold")
    )

    flashes = [
        "#ffffff",
        "#d9d6ff",
        "#85809d",
        "#242139",
        "#080812"
    ]

    def flash(i=0):

        if i < len(flashes):

            canvas.configure(bg=flashes[i])

            root.after(
                80,
                lambda: flash(i + 1)
            )

        else:

            canvas.configure(bg="#080812")
            canvas.delete(text)
            final_animation()

    flash()


# =========================================================
# FINAL SCREEN
# =========================================================

def final_animation():

    # Falling particles
    confetti = []

    for _ in range(120):

        x = random.randint(150, 750)
        y = random.randint(-150, 50)
        size = random.randint(2, 5)

        p = canvas.create_oval(
            x, y,
            x + size,
            y + size,
            fill="#ffffff",
            outline=""
        )

        confetti.append([
            p, x, y,
            random.uniform(2, 5)
        ])

    def fall():

        alive = False

        for p in confetti:

            p[2] += p[3]

            canvas.coords(
                p[0],
                p[1],
                p[2],
                p[1] + 4,
                p[2] + 4
            )

            if p[2] < HEIGHT:
                alive = True

        if alive:
            root.after(25, fall)

    fall()

    # Card
    canvas.create_rectangle(
        150, 95,
        750, 510,
        fill="#11111f",
        outline="#3a3455",
        width=2
    )

    title = canvas.create_text(
        WIDTH // 2,
        140,
        text="",
        fill="#85809d",
        font=("Segoe UI", 10, "bold")
    )

    # Typewriter title
    message = "MISSION ACCOMPLISHED"

    def type_title(i=0):

        if i <= len(message):

            canvas.itemconfig(
                title,
                text=message[:i]
            )

            root.after(
                55,
                lambda: type_title(i + 1)
            )

        else:

            reveal_message()

    type_title()


# =========================================================
# MESSAGE REVEAL
# =========================================================

def reveal_message():

    canvas.create_text(
        WIDTH // 2,
        205,
        text="🎉 FINALLY NO MIL GAYA! 🎉",
        fill="white",
        font=("Segoe UI", 27, "bold")
    )

    canvas.create_text(
        WIDTH // 2,
        270,
        text="Itni der se YES se bhaag rahe the…",
        fill="#c0bdcf",
        font=("Segoe UI", 15)
    )

    canvas.create_text(
        WIDTH // 2,
        300,
        text="🏃‍♀️💨 aur aakhirkaar NO mil hi gaya!",
        fill="#c0bdcf",
        font=("Segoe UI", 15)
    )

    wait = canvas.create_text(
        WIDTH // 2,
        345,
        text="",
        fill="white",
        font=("Segoe UI", 20, "bold")
    )

    words = [
        "B",
        "BU",
        "BUT",
        "BUT W",
        "BUT WA",
        "BUT WAI",
        "BUT WAIT",
        "BUT WAIT… 👀"
    ]

    def type_wait(i=0):

        if i < len(words):

            canvas.itemconfig(
                wait,
                text=words[i]
            )

            root.after(
                100,
                lambda: type_wait(i + 1)
            )

    type_wait()

    root.after(1200, reveal_gift)


# =========================================================
# GIFT REVEAL
# =========================================================

def reveal_gift():

    canvas.create_line(
        270, 375,
        630, 375,
        fill="#343049",
        width=1
    )

    gift = canvas.create_text(
        WIDTH // 2,
        405,
        text="🎁  GIFT CANCEL NAHI HUA HAI! 😈",
        fill="white",
        font=("Segoe UI", 18, "bold")
    )

    canvas.itemconfig(gift, state="hidden")

    # Pop-in
    def pop(i=0):

        if i < 6:
            canvas.itemconfig(gift, state="normal")

            # Slight size effect using different fonts
            sizes = [5, 9, 12, 15, 17, 18]

            canvas.itemconfig(
                gift,
                font=("Segoe UI", sizes[i], "bold")
            )

            root.after(
                60,
                lambda: pop(i + 1)
            )

    pop()

    canvas.create_text(
        WIDTH // 2,
        440,
        text="Rakhi hai bhai, bach nahi sakte! 🫶😂",
        fill="#aaa6bb",
        font=("Segoe UI", 14)
    )

    canvas.create_text(
        WIDTH // 2,
        475,
        text="✨  HAPPY RAKSHABANDHAN!  ✨",
        fill="white",
        font=("Segoe UI", 15, "bold")
    )


# =========================================================
# START
# =========================================================

animate_particles()
root.mainloop()
