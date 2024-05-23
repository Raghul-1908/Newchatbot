Import customtkinter as tk
import tkinter as kt
from tkinter import simpledialog
import json
import re

tk.set_appearance_mode("dark")

class Ai:
    @staticmethod
    def load_data(file_path):
        with open(file_path, 'r') as file:
            data = json.load(file)
        return data

    @staticmethod
    def save_data(data, file_path, user_input, new_answer):
        data[user_input] = new_answer
        with open(file_path, "w") as file:
            json.dump(data, file)

    @staticmethod
    def get_answer(question, data):
        if question in data:
            return data[question]
        else:
            return False

    @staticmethod
    def is_math_expression(expression):
        # A simple check to see if the expression looks like a math problem
        math_pattern = r"^\s*-?\d+(\.\d+)?\s*([+\-*/]\s*-?\d+(\.\d+)?\s*)+$"
        return re.match(math_pattern, expression) is not None

    @staticmethod
    def evaluate_math_expression(expression):
        try:
            # Safely evaluate the math expression
            result = eval(expression, {"__builtins__": {}})
            return str(result)
        except Exception as e:
            return "Error: Invalid mathematical expression."

    @staticmethod
    def main(question, chat_frame, entry):
        file_path = r"C:\Users\srmde\OneDrive\Desktop\chatbot\jsqa.txt"
        data = Ai.load_data(file_path)

        if question.lower() == 'quit':
            Ai.add_message(chat_frame, "GOODBYE!", "left")
            return

        if Ai.is_math_expression(question):
            answer = Ai.evaluate_math_expression(question)
        else:
            answer = Ai.get_answer(question, data)

        Ai.add_message(chat_frame, question, "right")  # User message

        if answer:
            Ai.add_message(chat_frame, answer, "left")  # Bot message
        else:
            Ai.add_message(chat_frame, "I'm sorry, I don't know the answer to that question.", "left")
            new_answer = simpledialog.askstring("Teach me", "Bot: Can you please teach me? What's the answer to that question?")
            if new_answer:
                Ai.save_data(data, file_path, question, new_answer)
                Ai.add_message(chat_frame, "Thank you for teaching me! Now I'll remember that for next time.", "left")

        entry.delete(0, tk.END)

    @staticmethod
    def add_message(chat_frame, message, side):
        message_frame = tk.CTkFrame(chat_frame, width=700, corner_radius=15, fg_color="gray10")
        message_frame.pack(fill='x', padx=10, pady=5, anchor='w' if side == 'left' else 'e')

        bubble_color = "blue" if side == 'right' else "green"
        message_bubble = tk.CTkLabel(message_frame, text=message, anchor='w', justify='left', text_color="white", fg_color=bubble_color, corner_radius=10, padx=10, pady=5)
        message_bubble.pack(fill='both', padx=5, pady=5)

class First:
    @staticmethod
    def dash_board():
        db = tk.CTk()
        db.geometry("870x540")
        db.resizable(width=False, height=False)

        c = kt.Canvas(db, width=870, height=540, bg="black")
        c.place(relx=-0.005, rely=-0.005)

        img = kt.PhotoImage(file=r"C:\Users\srmde\OneDrive\Desktop\chatbot\dboard.png")
        c.create_image(0, 0, anchor="nw", image=img)
        c.image = img  # Keep a reference to avoid garbage collection

        c.create_text(150, 50, text="SYNTHAI", font=("Arial", 40), fill="white")

        tex = """We’ve trained a model called SYNTHAI which interacts in a conversational way.
The dialogue format makes it possible for ChatGPT to answer followup questions,
admit its mistakes,In the event that it cannot provide a response,
you can assist it in learning. """
        c.create_text(420, 160, text=tex, font=("italic", 14), fill="lightblue")
        b1 = chr(126) + "SYNTHAI is sensitive to tweaks to the input phrasing"
        b2 = chr(126) + "The model is often excessively verbose and overuses certain phrases"
        b3 = chr(126) + "The model is created by trained people and has predetermined answers"
        c.create_text(370, 230, text=b1, font=("italic", 14), fill="gold")
        c.create_text(445, 250, text=b2, font=("italic", 14), fill="violet")
        c.create_text(454, 270, text=b3, font=("italic", 14), fill="red")

        btn = tk.CTkButton(master=db, width=100, height=40, corner_radius=7, hover_color="grey", text_color="gold", text="Get Started", fg_color="violet", command=lambda: First.nxt(db))
        btn.place(relx=0.5, rely=0.7, anchor=tk.CENTER)

        db.mainloop()

    @staticmethod
    def nxt(root):
        root.destroy()
        First.main()

    @staticmethod
    def main():
        base = tk.CTk()
        base.geometry("870x540")
        base.resizable(width=False, height=False)
        base.title("Synthai")

        title = tk.CTkLabel(master=base, text="Synthai", font=("Roboto", 32), text_color="White")
        title.place(relx=0.1, rely=0.03)

        logo = kt.PhotoImage(file=r"C:\Users\srmde\OneDrive\Desktop\chatbot\ai.png")
        logolabel = kt.Label(master=base, image=logo, bg="black")
        logolabel.place(relx=0.03, rely=0.02)
        logolabel.image = logo  # Keep a reference to avoid garbage collection

        chat_frame = tk.CTkScrollableFrame(master=base, width=800, height=400, bg_color="black")
        chat_frame.place(relx=0.05, rely=0.15)

        box = tk.CTkEntry(master=base, font=("Ariel", 16), width=600)
        box.place(relx=0.05, rely=0.9)

        send_button = tk.CTkButton(base, text="Send", height=30, width=70, command=lambda: Ai.main(box.get(), chat_frame, box), corner_radius=20)
        send_button.place(relx=0.85, rely=0.9)

        base.mainloop()

if __name__ == "__main__":
    First.dash_board()
