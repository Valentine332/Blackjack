# Blackjack
import java.util.ArrayList;
import java.util.Collections;
import java.util.Scanner;

public class BlackjackGame {
    private static final String[] SUITS = {"♠", "♥", "♦", "♣"};
    private static final String[] RANKS = {"2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A"};

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        while (true) {
            System.out.println("Welcome to Blackjack!");
            System.out.println("Press Enter to start a new round or type 'exit' to quit.");
            String input = scanner.nextLine();
            if (input.equalsIgnoreCase("exit")) {
                break;
            }

            playRound(scanner);
        }

        scanner.close();
    }

    private static void playRound(Scanner scanner) {
        ArrayList<String> deck = initializeDeck();
        Collections.shuffle(deck);

        ArrayList<String> playerHand = new ArrayList<>();
        ArrayList<String> dealerHand = new ArrayList<>();

        // 发两张牌给玩家和庄家
        playerHand.add(drawCard(deck));
        dealerHand.add(drawCard(deck));
        playerHand.add(drawCard(deck));
        dealerHand.add(drawCard(deck));

        // 显示初始手牌
        System.out.println("Your hand: " + playerHand);
        System.out.println("Dealer's hand: " + dealerHand.get(0) + ", [Hidden Card]");

        // 玩家回合
        while (true) {
            System.out.print("Do you want to Hit (H) or Stand (S)? ");
            String decision = scanner.nextLine().toUpperCase();
            if (decision.equals("H")) {
                playerHand.add(drawCard(deck));
                System.out.println("Your hand: " + playerHand);
                if (getHandValue(playerHand) > 21) {
                    System.out.println("Busted! You lose.");
                    return;
                }
            } else if (decision.equals("S")) {
                break;
            } else {
                System.out.println("Invalid input! Please enter H or S.");
            }
        }

        // 庄家回合
        while (getHandValue(dealerHand) < 17) {
            dealerHand.add(drawCard(deck));
        }

        // 显示最终手牌
        System.out.println("Your hand: " + playerHand);
        System.out.println("Dealer's hand: " + dealerHand);
        
        // 判断胜负
        int playerValue = getHandValue(playerHand);
        int dealerValue = getHandValue(dealerHand);
        if (playerValue > 21) {
            System.out.println("Busted! You lose.");
        } else if (dealerValue > 21 || playerValue > dealerValue) {
            System.out.println("Congratulations! You win.");
        } else if (playerValue == dealerValue) {
            System.out.println("It's a tie.");
        } else {
            System.out.println("Dealer wins.");
        }
    }

    private static ArrayList<String> initializeDeck() {
        ArrayList<String> deck = new ArrayList<>();
        for (String suit : SUITS) {
            for (String rank : RANKS) {
                deck.add(rank + suit);
            }
        }
        return deck;
    }

    private static String drawCard(ArrayList<String> deck) {
        return deck.remove(0);
    }

    private static int getHandValue(ArrayList<String> hand) {
        int value = 0;
        int numAces = 0;
        for (String card : hand) {
            String rank = card.substring(0, card.length() - 1);
            if (rank.equals("J") || rank.equals("Q") || rank.equals("K")) {
                value += 10;
            } else if (rank.equals("A")) {
                value += 11;
                numAces++;
            } else {
                value += Integer.parseInt(rank);
            }
        }
        // 如果手牌中有A且总点数超过21，将A视为1
        while (value > 21 && numAces > 0) {
            value -= 10;
            numAces--;
        }
        return value;
    }
}
