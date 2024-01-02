using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;

class Program
{
    static List<Player> leaderboard = new List<Player>();

    static void Main()
    {
        while (true)
        {
            HomeScreen();

            string choice = Console.ReadLine();

            switch (choice)
            {
                case "1":
                    PlayGame();
                    break;
                case "2":
                    DisplayAboutScreen();
                    break;
                case "3":
                    DisplayLeaderboard();
                    break;
                case "4":
                    Console.WriteLine("Goodbye!");
                    return;
                default:
                    Console.WriteLine("Invalid choice. Please enter 1, 2, 3, or 4.");
                    break;
            }
        }
    }

    static void HomeScreen()
    {
        Console.ForegroundColor = ConsoleColor.Cyan;
        Console.Clear();
        Console.WriteLine("");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 2 - 5) + "██     ██  ██████  ██████  ██████         ██████  ");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 2 - 5) + "██     ██ ██    ██ ██   ██ ██   ██       ██       ");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 2 - 5) + "██  █  ██ ██    ██ ██████  ██   ██ █████ ██   ███ ");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 2 - 5) + "██ ███ ██ ██    ██ ██   ██ ██   ██       ██    ██ ");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 2 - 5) + " ███ ███   ██████  ██   ██ ██████         ██████  ");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.ResetColor();
        Console.WriteLine("\n" + "".PadLeft(Console.WindowWidth / 2 - 1) + "1. Play Game");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 2 - 1) + "2. About");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 2 - 1) + "3. Leaderboard");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 2 - 1) + "4. Exit");
    }

    static void DisplayAboutScreen()
    {
        Console.Clear();
        Console.ForegroundColor = ConsoleColor.Cyan;
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 5) + " █████╗ ██████╗  ██████╗ ██╗   ██╗████████╗");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 5) + "██╔══██╗██╔══██╗██╔═══██╗██║   ██║╚══██╔══╝");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 5) + "███████║██████╔╝██║   ██║██║   ██║   ██║   ");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 5) + "██╔══██║██╔══██╗██║   ██║██║   ██║   ██║   ");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 5) + "██║  ██║██████╔╝╚██████╔╝╚██████╔╝   ██║   ");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 5) + "╚═╝  ╚═╝╚═════╝  ╚═════╝  ╚═════╝    ╚═╝   ");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.ResetColor();
        Console.WriteLine("\nWORD G is a word-guessing game. You have to guess the letters of a hidden word.");
        Console.WriteLine("You have a limited number of attempts to guess the word. Each incorrect guess reduces your attempts.");
        Console.WriteLine("\nPress Enter to return to the main menu.");
        Console.ReadLine();
    }

    static void PlayGame()
    {
        Console.Clear();
        Console.Write("Enter your name: ");
        string playerName = Console.ReadLine();

        int score = 0;

        do
        {
            string[] words = { "programming", "flowchart", "computer", "console", "developer", "integers", "database", "algorithm", "looping", "modulus", "debugging", "syntax", "deadline", "cycle", "structure", "" };
            Random random = new Random();
            string wordToGuess = words[random.Next(0, words.Length)];
            char[] guessedWord = new char[wordToGuess.Length];
            int maxAttempts = 6;
            int attemptsLeft = maxAttempts;

            for (int i = 0; i < guessedWord.Length; i++)
            {
                guessedWord[i] = '_';
            }

            while (attemptsLeft > 0)
            {
                Console.Clear();
                DisplayGameHeader(attemptsLeft);
                Console.WriteLine("Word: " + new string(guessedWord));

                Console.Write("Guess a letter: ");
                char guess = char.ToLower(Console.ReadKey().KeyChar);

                if (!char.IsLetter(guess))
                {
                    Console.WriteLine("\nInvalid input. Please enter a letter.");
                    continue;
                }

                if (wordToGuess.Contains(guess))
                {
                    for (int i = 0; i < wordToGuess.Length; i++)
                    {
                        if (wordToGuess[i] == guess && guessedWord[i] == '_')
                        {
                            guessedWord[i] = guess;
                            score++;
                        }
                    }
                }
                else
                {
                    attemptsLeft--;
                    DisplayExecutionEffect();
                }

                if (new string(guessedWord) == wordToGuess)
                {
                    Console.WriteLine("\nWord-G");
                    Console.WriteLine("Word: " + new string(guessedWord));
                    Console.ForegroundColor = ConsoleColor.Blue;
                    Console.WriteLine($"Congratulations, {playerName}! You guessed the word. Your score is: {score}");
                    Console.ResetColor();
                    break;
                }
            }

            if (attemptsLeft == 0)
            {
                Console.Clear();
                DisplayGameHeader(0);
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine($"Sorry, {playerName}, you're out of attempts. Your score is: {score}");
                Console.ResetColor();
                Console.WriteLine("The word was: " + wordToGuess);
            }

            Console.WriteLine("Press Enter to play again or Esc to return to the main menu.");

            // Update leaderboard
            if (!leaderboard.Any(p => p.Name == playerName) || leaderboard.First(p => p.Name == playerName).Score < score)
            {
                UpdateLeaderboard(playerName, score);
            }

        } while (Console.ReadKey().Key != ConsoleKey.Escape);
    }

    static void UpdateLeaderboard(string playerName, int score)
    {
        var existingPlayer = leaderboard.FirstOrDefault(p => p.Name == playerName);

        if (existingPlayer != null)
        {
            existingPlayer.Score = score;
        }
        else
        {
            leaderboard.Add(new Player { Name = playerName, Score = score });
        }
    }

    static void DisplayLeaderboard()
    {
        Console.Clear();
        Console.WriteLine("\nLeaderboard:");
        if (leaderboard.Count > 0)
        {
            int rank = 1;
            foreach (var player in leaderboard.OrderBy(p => p.Score))
            {
                Console.WriteLine($"{rank}. {player.Name} - {player.Score} points");
                rank++;
            }
        }
        else
        {
            Console.WriteLine("No entries in the leaderboard yet.");
        }

        Console.WriteLine("\nPress Enter to return to the main menu.");
        Console.ReadLine();
    }

    static void DisplayExecutionEffect()
    {
        for (int i = 0; i < 3; i++)
        {
            Console.Clear();

            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Oops! Wrong answer..");
            Console.ResetColor();
            Thread.Sleep(500);
        }
        Console.Clear();
    }

    static void DisplayGameHeader(int attemptsLeft)
    {
        Console.ForegroundColor = ConsoleColor.Cyan;
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 7) + "██╗    ██╗ ██████╗ ██████╗ ██████╗        ██████╗ ");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 7) + "██║    ██║██╔═══██╗██╔══██╗██╔══██╗      ██╔════╝ ");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 7) + "██║ █╗ ██║██║   ██║██████╔╝██║  ██║█████╗██║  ███╗");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 7) + "██║███╗██║██║   ██║██╔══██╗██║  ██║╚════╝██║   ██║");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 7) + "╚███╔███╔╝╚██████╔╝██║  ██║██████╔╝      ╚██████╔╝");
        Console.WriteLine("".PadLeft(Console.WindowWidth / 3 - 7) + " ╚══╝╚══╝  ╚═════╝ ╚═╝  ╚═╝╚═════╝        ╚═════╝ ");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.WriteLine("");
        Console.ResetColor();
        Console.ForegroundColor = ConsoleColor.Green;
        Console.Write("Attempts left: ");
        for (int i = 0; i < attemptsLeft; i++)
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Write("* ");
            Console.ResetColor();
        }
        Console.WriteLine();
        Console.ResetColor();
    }
}

class Player
{
    public string Name { get; set; }
    public int Score { get; set; }
}
