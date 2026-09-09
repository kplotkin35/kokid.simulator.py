# kokid.simulator.py

"""
KOKID - Worm + Virus Behavioral Simulator
Educational cybersecurity project

SAFE:
- No network connections
- No real file modification
- No persistence
- No destructive actions
- All activity is simulated in memory
"""

import tkinter as tk
from tkinter import ttk
import random


class KokidSimulator:
    def __init__(self, root):
        self.root = root
        self.root.title("KOKID | Cybersecurity Defense Simulator")
        self.root.geometry("1000x700")

        self.running = False
        self.infected = set()
        self.quarantined = set()
        self.alerts = []
        self.step = 0

        self.computers = {
            "PC-01": ["report.docx", "notes.txt"],
            "PC-02": ["assignment.pdf", "data.csv"],
            "PC-03": ["presentation.pptx", "image.jpg"],
            "PC-04": ["research.docx", "backup.zip"],
            "PC-05": ["project.py", "database.db"],
            "PC-06": ["budget.xlsx", "photos.jpg"],
            "PC-07": ["server.log", "config.ini"],
            "PC-08": ["backup.zip", "archive.tar"],
        }

        self.build_interface()
        self.draw_network()

    def build_interface(self):
        title = ttk.Label(
            self.root,
            text="KOKID — WORM + VIRUS BEHAVIOR SIMULATOR",
            font=("Arial", 18, "bold")
        )
        title.pack(pady=10)

        subtitle = ttk.Label(
            self.root,
            text="Educational simulation • No real files or networks affected"
        )
        subtitle.pack()

        controls = ttk.Frame(self.root)
        controls.pack(pady=10)

        ttk.Button(
            controls,
            text="Start Simulation",
            command=self.start
        ).grid(row=0, column=0, padx=5)

        ttk.Button(
            controls,
            text="Next Step",
            command=self.next_step
        ).grid(row=0, column=1, padx=5)

        ttk.Button(
            controls,
            text="Quarantine All",
            command=self.quarantine_all
        ).grid(row=0, column=2, padx=5)

        ttk.Button(
            controls,
            text="Reset",
            command=self.reset
        ).grid(row=0, column=3, padx=5)

        self.status = ttk.Label(
            self.root,
            text="Status: Ready"
        )
        self.status.pack()

        self.canvas = tk.Canvas(
            self.root,
            width=950,
            height=300,
            bg="white"
        )
        self.canvas.pack(pady=10)

        stats = ttk.Frame(self.root)
        stats.pack(pady=5)

        self.infected_label = ttk.Label(stats, text="Infected: 0")
        self.infected_label.grid(row=0, column=0, padx=20)

        self.alert_label = ttk.Label(stats, text="IDS Alerts: 0")
        self.alert_label.grid(row=0, column=1, padx=20)

        self.score_label = ttk.Label(stats, text="Threat Score: 0/100")
        self.score_label.grid(row=0, column=2, padx=20)

        self.log = tk.Text(
            self.root,
            height=12,
            width=115,
            state="disabled"
        )
        self.log.pack(pady=10)

    def write_log(self, message):
        self.log.config(state="normal")
        self.log.insert(tk.END, message + "\n")
        self.log.see(tk.END)
        self.log.config(state="disabled")

    def draw_network(self):
        self.canvas.delete("all")

        positions = {
            "PC-01": (100, 150),
            "PC-02": (250, 70),
            "PC-03": (250, 230),
            "PC-04": (450, 70),
            "PC-05": (450, 230),
            "PC-06": (650, 70),
            "PC-07": (650, 230),
            "PC-08": (850, 150),
        }

        connections = [
            ("PC-01", "PC-02"),
            ("PC-01", "PC-03"),
            ("PC-02", "PC-04"),
            ("PC-03", "PC-05"),
            ("PC-04", "PC-06"),
            ("PC-05", "PC-07"),
            ("PC-06", "PC-08"),
            ("PC-07", "PC-08"),
        ]

        for a, b in connections:
            x1, y1 = positions[a]
            x2, y2 = positions[b]
            self.canvas.create_line(
                x1, y1, x2, y2,
                fill="gray",
                width=2
            )

        for name, (x, y) in positions.items():
            if name in self.quarantined:
                color = "gray"
            elif name in self.infected:
                color = "red"
            else:
                color = "lightgreen"

            self.canvas.create_oval(
                x - 35, y - 25,
                x + 35, y + 25,
                fill=color,
                outline="black",
                width=2
            )

            self.canvas.create_text(
                x, y,
                text=name,
                font=("Arial", 10, "bold")
            )

        self.canvas.create_text(
            475, 285,
            text="Green = Normal   Red = Simulated Infection   Gray = Quarantined",
            font=("Arial", 10)
        )

    def update_stats(self):
        self.infected_label.config(
            text=f"Infected: {len(self.infected)}"
        )

        self.alert_label.config(
            text=f"IDS Alerts: {len(self.alerts)}"
        )

        score = min(
            100,
            len(self.infected) * 12 + len(self.alerts) * 5
        )

        self.score_label.config(
            text=f"Threat Score: {score}/100"
        )

    def start(self):
        if not self.running:
            self.running = True
            self.status.config(text="Status: Simulation Running")
            self.write_log("[KOKID] Simulation started.")
            self.infected.add("PC-01")
            self.write_log(
                "[VIRUS] PC-01 selected as simulated initial infection."
            )
            self.draw_network()
            self.update_stats()

    def next_step(self):
        if not self.running:
            self.write_log("[SYSTEM] Start the simulation first.")
            return

        targets = [
            name for name in self.computers
            if name not in self.infected
            and name not in self.quarantined
        ]

        if not targets:
            self.write_log("[SYSTEM] No available targets.")
            return

        target = random.choice(targets)

        self.infected.add(target)
        self.alerts.append(target)
        self.step += 1

        self.write_log(
            f"[WORM] SIMULATION: Propagation event "
            f"detected toward {target}."
        )

        self.write_log(
            f"[IDS] ALERT #{len(self.alerts)}: "
            f"Suspicious propagation pattern detected."
        )

        self.draw_network()
        self.update_stats()

    def quarantine_all(self):
        if not self.infected:
            self.write_log("[SYSTEM] No infected systems to quarantine.")
            return

        for name in list(self.infected):
            self.quarantined.add(name)

        self.write_log(
            f"[DEFENSE] {len(self.quarantined)} system(s) quarantined."
        )

        self.draw_network()
        self.update_stats()

    def reset(self):
        self.running = False
        self.infected.clear()
        self.quarantined.clear()
        self.alerts.clear()
        self.step = 0

        self.status.config(text="Status: Ready")

        self.log.config(state="normal")
        self.log.delete("1.0", tk.END)
        self.log.config(state="disabled")

        self.write_log("[SYSTEM] Simulator reset.")
        self.draw_network()
        self.update_stats()


if __name__ == "__main__":
    root = tk.Tk()
    app = KokidSimulator(root)
    root.mainloop()
