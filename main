import curses
import time
from random import randint

class ReactorGame:
    def __init__(self):
        self.power = 0.0
        self.temperature = 150
        self.stability = 100.0
        self.fuel_type = 'deuterium'
        self.resources = {'SP': 100, 'Energy': 500, 'Credits': 200}
        self.screen = None
        self.plasma_phase = 0
        self.current_screen = 'main'  # 'main', 'resources', 'minigame'
        self.plasma_frames = [
            "  (  )  ",
            " (~~~~) ",
            "(~~~~~~)",
            " (~~~~) ",
            "  (  )  "
        ]

    def init_colors(self):
        curses.init_pair(1, curses.COLOR_CYAN, curses.COLOR_BLACK)
        curses.init_pair(2, curses.COLOR_GREEN, curses.COLOR_BLACK)
        curses.init_pair(3, curses.COLOR_RED, curses.COLOR_BLACK)
        curses.init_pair(4, curses.COLOR_MAGENTA, curses.COLOR_BLACK)
        curses.init_pair(5, curses.COLOR_YELLOW, curses.COLOR_BLACK)

    def draw_main_screen(self):
        height, width = self.screen.getmaxyx()

        # Реактор с анимацией
        self.plasma_phase = (self.plasma_phase + 1) % len(self.plasma_frames)
        plasma = self.plasma_frames[self.plasma_phase]

        reactor = [
            "   ╔══════════════════╗   ",
            "   ║  ░▒▓ PLASMA ▓▒░  ║   ",
            f"   ║ ▒▓▒{plasma}▒ ▒▓  ║   ",
            "   ║ ▓▒░       ░▒▓ ░▓ ║   ",
            "   ╚══════════════════╝   "
        ]

        for i, line in enumerate(reactor):
            try:
                self.screen.addstr(height//2-3+i, width//2-15, line, curses.color_pair(1))
            except curses.error:
                pass

        # HUD
        try:
            self.screen.addstr(2, 2, f"POWER: {self.power:.1f}/4.5 kW", curses.color_pair(2))
            self.screen.addstr(3, 2, f"TEMP: {self.temperature}M °C", curses.color_pair(3))

            stability_int = int(self.stability)
            stability_bar = f"STABILITY: [{'█' * (stability_int//10)}{' ' * (10-stability_int//10)}]"
            color = curses.color_pair(2) if stability_int > 30 else curses.color_pair(3)
            self.screen.addstr(4, 2, stability_bar, color)

            # Меню
            buttons = ["[1] Research", "[2] Resources", "[3] Balance Plasma", "[q] Quit"]
            for i, btn in enumerate(buttons):
                attr = curses.A_BOLD if i == 1 and self.current_screen == 'resources' else 0
                self.screen.addstr(6+i, 2, btn, curses.color_pair(4) | attr)
        except curses.error:
            pass

    def draw_resources_screen(self):
        try:
            self.screen.clear()
            self.screen.addstr(2, 2, "=== RESOURCES ===", curses.color_pair(4) | curses.A_BOLD)

            for i, (name, amount) in enumerate(self.resources.items()):
                self.screen.addstr(4+i, 2, f"{name}: {amount}", curses.color_pair(2))

            self.screen.addstr(10, 2, "[b] Back to main", curses.color_pair(4))
        except curses.error:
            pass

    def plasma_minigame(self):
        start_time = time.time()
        while time.time() - start_time < 10:  # 10 секунд на мини-игру
            try:
                self.screen.clear()
                self.screen.addstr(2, 2, "BALANCE PLASMA (Use LEFT/RIGHT arrows)", curses.A_BOLD)

                # Генерация волн
                wave = "~" * randint(10, 30)
                self.screen.addstr(10, 5, wave, curses.color_pair(3))

                # Обработка ввода
                key = self.screen.getch()
                if key == curses.KEY_LEFT:
                    self.stability = min(100.0, self.stability + 5.0)
                elif key == curses.KEY_RIGHT:
                    self.stability = max(0.0, self.stability - 5.0)

                # Отображение стабильности
                stability_bar = f"[{'█' * (int(self.stability)//10)}{' ' * (10-int(self.stability)//10)}]"
                self.screen.addstr(12, 2, stability_bar, curses.color_pair(2))

                self.screen.refresh()
                time.sleep(0.1)
            except (curses.error, KeyboardInterrupt):
                break

        self.current_screen = 'main'

    def update_state(self):
        # Автоматическое обновление параметров
        self.power = min(4.5, self.power + 0.01)
        self.temperature += randint(-1, 1)
        self.stability = max(0.0, min(100.0, self.stability - 0.2))

    def run(self):
        try:
            self.screen = curses.initscr()
            curses.noecho()
            curses.cbreak()
            curses.curs_set(0)
            self.screen.keypad(True)
            self.screen.nodelay(True)

            if curses.has_colors():
                curses.start_color()
                self.init_colors()

            while True:
                self.screen.erase()

                if self.current_screen == 'main':
                    self.update_state()
                    self.draw_main_screen()
                elif self.current_screen == 'resources':
                    self.draw_resources_screen()

                key = self.screen.getch()
                if key == ord('q'):
                    break
                elif key == ord('1'):
                    self.current_screen = 'main'
                elif key == ord('2'):
                    self.current_screen = 'resources'
                elif key == ord('3') and self.current_screen == 'main':
                    self.plasma_minigame()
                elif key == ord('b') and self.current_screen == 'resources':
                    self.current_screen = 'main'

                self.screen.refresh()
                time.sleep(0.05)

        finally:
            curses.nocbreak()
            self.screen.keypad(False)
            curses.echo()
            curses.endwin()

if __name__ == "__main__":
    game = ReactorGame()
    game.run()