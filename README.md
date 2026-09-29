from PIL import Image, ImageDraw, ImageFont
import random
import os
import math

# ==============================
# GITHUB HACKER PROFILE GENERATOR
# ==============================

WIDTH = 1200
HEIGHT = 500
FRAMES = 80
FPS = 15

OUTPUT = "github_hacker_banner.gif"

BG = (2, 5, 8)
GREEN = (0, 255, 120)
DARK_GREEN = (0, 100, 50)
WHITE = (220, 255, 235)

# ------------------------------
# Fonts
# ------------------------------

def get_font(size):
    paths = [
        "C:/Windows/Fonts/consola.ttf",
        "C:/Windows/Fonts/consolab.ttf",
        "/usr/share/fonts/truetype/dejavu/DejaVuSansMono.ttf"
    ]

    for path in paths:
        if os.path.exists(path):
            return ImageFont.truetype(path, size)

    return ImageFont.load_default()


FONT_BIG = get_font(58)
FONT_MED = get_font(26)
FONT_SMALL = get_font(18)

# ------------------------------
# Matrix rain
# ------------------------------

chars = "01ABCDEFGHIJKLMNOPQRSTUVWXYZ#$%&<>[]{}"

columns = WIDTH // 18

drops = [
    random.randint(-30, HEIGHT // 18)
    for _ in range(columns)
]

speeds = [
    random.randint(1, 4)
    for _ in range(columns)
]

# ------------------------------
# Fake terminal data
# ------------------------------

terminal_lines = [
    "$ ./system_boot",
    "[ OK ] Initializing kernel",
    "[ OK ] Loading developer profile",
    "[ OK ] Connecting to GitHub",
    "[ OK ] Projects loaded",
    "[ OK ] Neural systems online",
    "[ OK ] Computer vision online",
    "[ OK ] Python environment ready",
    "",
    "$ whoami",
    "developer",
    "",
    "$ status",
    "SYSTEM ONLINE",
]

# ------------------------------
# Generate frames
# ------------------------------

frames = []

for frame_number in range(FRAMES):

    img = Image.new(
        "RGB",
        (WIDTH, HEIGHT),
        BG
    )

    draw = ImageDraw.Draw(img)

    # ==========================
    # MATRIX BACKGROUND
    # ==========================

    for x in range(columns):

        x_pos = x * 18

        for i in range(12):

            y = (drops[x] - i) * 18

            if 0 <= y < HEIGHT:

                char = random.choice(chars)

                brightness = max(
                    40,
                    255 - i * 20
                )

                color = (
                    0,
                    brightness,
                    int(brightness * 0.45)
                )

                draw.text(
                    (x_pos, y),
                    char,
                    font=FONT_SMALL,
                    fill=color
                )

        drops[x] += speeds[x]

        if drops[x] * 18 > HEIGHT + 200:
            drops[x] = random.randint(-20, 0)

    # ==========================
    # DARK OVERLAY
    # ==========================

    overlay = Image.new(
        "RGBA",
        (WIDTH, HEIGHT),
        (0, 0, 0, 150)
    )

    img = Image.alpha_composite(
        img.convert("RGBA"),
        overlay
    )

    draw = ImageDraw.Draw(img)

    # ==========================
    # TOP TERMINAL BAR
    # ==========================

    draw.rectangle(
        (35, 25, WIDTH - 35, 65),
        outline=DARK_GREEN,
        width=2
    )

    draw.ellipse(
        (50, 38, 61, 49),
        fill=(255, 70, 70)
    )

    draw.ellipse(
        (70, 38, 81, 49),
        fill=(255, 210, 70)
    )

    draw.ellipse(
        (90, 38, 101, 49),
        fill=GREEN
    )

    draw.text(
        (125, 32),
        "user@github: ~/profile",
        font=FONT_SMALL,
        fill=GREEN
    )

    # ==========================
    # MAIN TITLE
    # ==========================

    title = "ACCESS GRANTED"

    # Slight animated glitch
    glitch = 2 if frame_number % 12 < 2 else 0

    draw.text(
        (WIDTH // 2 - 270 + glitch, 105),
        title,
        font=FONT_BIG,
        fill=GREEN
    )

    draw.text(
        (WIDTH // 2 - 268, 107),
        title,
        font=FONT_BIG,
        fill=(0, 80, 40)
    )

    # ==========================
    # SCAN LINE
    # ==========================

    scan_y = 85 + (frame_number * 7) % 360

    draw.line(
        (70, scan_y, WIDTH - 70, scan_y),
        fill=(0, 255, 120, 180),
        width=2
    )

    # ==========================
    # TERMINAL WINDOW
    # ==========================

    box_x = 80
    box_y = 205
    box_w = 1040
    box_h = 220

    draw.rounded_rectangle(
        (
            box_x,
            box_y,
            box_x + box_w,
            box_y + box_h
        ),
        radius=12,
        outline=DARK_GREEN,
        width=2
    )

    # ==========================
    # TERMINAL TEXT
    # ==========================

    visible_lines = min(
        len(terminal_lines),
        7 + frame_number // 8
    )

    start = max(
        0,
        visible_lines - 7
    )

    y = box_y + 18

    for line in terminal_lines[start:visible_lines]:

        # Blinking cursor
        cursor = ""

        if line == terminal_lines[visible_lines - 1]:
            if frame_number % 10 < 5:
                cursor = "█"

        draw.text(
            (box_x + 25, y),
            line + cursor,
            font=FONT_SMALL,
            fill=WHITE if line.startswith("[") else GREEN
        )

        y += 27

    # ==========================
    # STATUS
    # ==========================

    pulse = int(
        120 + 100 * abs(
            math.sin(frame_number / 8)
        )
    )

    draw.ellipse(
        (
            WIDTH - 230,
            112,
            WIDTH - 215,
            127
        ),
        fill=(0, pulse, 70)
    )

    draw.text(
        (WIDTH - 200, 105),
        "ONLINE",
        font=FONT_MED,
        fill=GREEN
    )

    # ==========================
    # FOOTER
    # ==========================

    footer = (
        "PYTHON  //  GITHUB  //  CODE  //  CREATE"
    )

    draw.text(
        (
            WIDTH // 2 - 250,
            450
        ),
        footer,
        font=FONT_SMALL,
        fill=DARK_GREEN
    )

    frames.append(
        img.convert("P", palette=Image.ADAPTIVE)
    )

# ==============================
# SAVE GIF
# ==============================

frames[0].save(
    OUTPUT,
    save_all=True,
    append_images=frames[1:],
    duration=int(1000 / FPS),
    loop=0,
    optimize=False
)

print()
print("=" * 55)
print(" GITHUB HACKER PROFILE GENERATED")
print("=" * 55)
print()
print(f"File: {OUTPUT}")
print(f"Frames: {FRAMES}")
print(f"FPS: {FPS}")
print()
print("Done!")
