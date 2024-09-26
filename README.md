import random

# تعريف كلاس البطاقة
class Card:
    def __init__(self, suit, rank):
        self.suit = suit
        self.rank = rank
        self.value = self.get_value()

    def get_value(self):
        if self.rank in ['J', 'Q', 'K']:
            return 10
        elif self.rank == 'A':
            return 11  # سيعدل لاحقاً إذا تجاوزت 21
        else:
            return int(self.rank)

    def __str__(self):
        return f"{self.rank} of {self.suit}"

# تعريف كلاس سطح اللعب
class Deck:
    suits = ['Hearts', 'Diamonds', 'Clubs', 'Spades']
    ranks = [str(r) for r in range(2, 11)] + ['J', 'Q', 'K', 'A']

    def __init__(self):
        self.cards = [Card(suit, rank) for suit in self.suits for rank in self.ranks]
        random.shuffle(self.cards)

    def deal_card(self):
        return self.cards.pop()

# تعريف كلاس اليد
class Hand:
    def __init__(self):
        self.cards = []

    def add_card(self, card):
        self.cards.append(card)

    def calculate_value(self):
        value = sum(card.value for card in self.cards)
        aces = sum(1 for card in self.cards if card.rank == 'A')

        while value > 21 and aces:
            value -= 10
            aces -= 1
        return value

    def __str__(self):
        return ", ".join(str(card) for card in self.cards)

# تعريف كلاس اللعبه
class Game:
    def __init__(self):
        self.deck = Deck()
        self.player_hand = Hand()
        self.dealer_hand = Hand()

    def start_game(self):
        # توزيع البطاقات
        for _ in range(2):
            self.player_hand.add_card(self.deck.deal_card())
            self.dealer_hand.add_card(self.deck.deal_card())

        print(f"يد اللاعب: {self.player_hand} (القيمة: {self.player_hand.calculate_value()})")
        print(f"يد الخصم: [{self.dealer_hand.cards[0]}, ?]")

        self.player_turn()
        if self.player_hand.calculate_value() <= 21:
            self.dealer_turn()
        self.end_game()

    def player_turn(self):
        while True:
            action = input("هل تريد السحب (H) أو الوقوف (S)؟ ").strip().upper()
            if action == 'H':
                self.player_hand.add_card(self.deck.deal_card())
                print(f"يد اللاعب: {self.player_hand} (القيمة: {self.player_hand.calculate_value()})")
                if self.player_hand.calculate_value() > 21:
                    print("لقد تجاوزت 21! خسرت.")
                    return
            elif action == 'S':
                break

    def dealer_turn(self):
        while self.dealer_hand.calculate_value() < 17:
            self.dealer_hand.add_card(self.deck.deal_card())
        print(f"يد الخصم: {self.dealer_hand} (القيمة: {self.dealer_hand.calculate_value()})")

    def end_game(self):
        player_value = self.player_hand.calculate_value()
        dealer_value = self.dealer_hand.calculate_value()

        if dealer_value > 21 or player_value > dealer_value:
            print("نجحت! لقد فزت.")
        elif player_value < dealer_value:
            print("، خسرت.")
        else:
            print(" تعادل!")

# تشغيل اللعبة
if __name__ == "__main__":
    game = Game()
    game.start_game()
