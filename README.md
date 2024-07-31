# hangmangame
import random

class Hangman:
    def __init__(self, word_list, max_incorrect_guesses=6):
        self.word_list = word_list
        self.word = random.choice(self.word_list).upper()
        self.guesses = set()
        self.correct_guesses = set()
        self.incorrect_guesses = 0
        self.max_incorrect_guesses = max_incorrect_guesses
        self.hints_used = 0

    def guess(self, letter):
        letter = letter.upper()
        if letter in self.guesses:
            return False
        self.guesses.add(letter)
        if letter in self.word:
            self.correct_guesses.add(letter)
        else:
            self.incorrect_guesses += 1
        return True

    def get_display_word(self):
        return " ".join([letter if letter in self.correct_guesses else "_" for letter in self.word])

    def is_won(self):
        return all(letter in self.correct_guesses for letter in self.word)

    def is_lost(self):
        return self.incorrect_guesses >= self.max_incorrect_guesses

    def get_hangman_graphics(self):
        stages = [
            """
               ------
               |    |
                    |
                    |
                    |
                    |
            --------
            """,
            """
               ------
               |    |
               O    |
                    |
                    |
                    |
            --------
            """,
            """
               ------
               |    |
               O    |
               |    |
                    |
                    |
            --------
            """,
            """
               ------
               |    |
               O    |
              /|    |
                    |
                    |
            --------
            """,
            """
               ------
               |    |
               O    |
              /|\\   |
                    |
                    |
            --------
            """,
            """
               ------
               |    |
               O    |
              /|\\   |
              /     |
                    |
            --------
            """,
            """
               ------
               |    |
               O    |
              /|\\   |
              / \\   |
                    |
            --------
            """
        ]
        return stages[self.incorrect_guesses]

    def use_hint(self):
        available_letters = [letter for letter in self.word if letter not in self.correct_guesses]
        if available_letters:
            hint = random.choice(available_letters)
            self.correct_guesses.add(hint)
            self.hints_used += 1
            return hint
        return None

word_categories = {
    "Animals": ["ELEPHANT", "GIRAFFE", "KANGAROO", "ZEBRA"],
    "Countries": ["CANADA", "AUSTRALIA", "INDIA", "BRAZIL"],
    "Movies": ["INCEPTION", "GLADIATOR", "TITANIC", "AVATAR"]
}

def choose_category():
    print("Choose a category:")
    for i, category in enumerate(word_categories.keys()):
        print(f"{i + 1}. {category}")
    choice = int(input("Enter the number of your choice: ")) - 1
    category = list(word_categories.keys())[choice]
    return word_categories[category]

def choose_difficulty():
    print("Choose a difficulty level:")
    print("1. Easy (10 incorrect guesses)")
    print("2. Medium (6 incorrect guesses)")
    print("3. Hard (4 incorrect guesses)")
    choice = int(input("Enter the number of your choice: "))
    if choice == 1:
        return 10
    elif choice == 2:
        return 6
    elif choice == 3:
        return 4

def multiplayer_mode():
    word = input("Player 1, enter the word for Player 2 to guess: ").upper()
    game = Hangman([word])
    print("\n" * 50)  # Clear the screen
    while not (game.is_won() or game.is_lost()):
        print(game.get_display_word())
        print(game.get_hangman_graphics())
        guess = input("Player 2, enter a letter (or type 'hint' for a hint): ").upper()
        if guess == "HINT":
            hint = game.use_hint()
            if hint:
                print(f"Hint: The word contains the letter '{hint}'.")
            else:
                print("No hints available.")
        else:
            game.guess(guess)
    if game.is_won():
        print("Player 2 wins!")
    else:
        print("Player 1 wins! The word was:", game.word)

# Main Game Loop
if __name__ == "__main__":
    mode = input("Choose mode (single/multi): ").lower()
    if mode == "multi":
        multiplayer_mode()
    else:
        word_list = choose_category()
        max_incorrect_guesses = choose_difficulty()
        game = Hangman(word_list, max_incorrect_guesses)
        while not (game.is_won() or game.is_lost()):
            print(game.get_display_word())
            print(game.get_hangman_graphics())
            guess = input("Enter a letter (or type 'hint' for a hint): ").upper()
            if guess == "HINT":
                hint = game.use_hint()
                if hint:
                    print(f"Hint: The word contains the letter '{hint}'.")
                else:
                    print("No hints available.")
            else:
                game.guess(guess)
        if game.is_won():
            print("Congratulations! You've won!")
        else:
            print("Sorry, you've lost. The word was:", game.word)
