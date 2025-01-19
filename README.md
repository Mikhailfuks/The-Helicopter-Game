using System;
using System.Collections.Generic;
using System.Threading;

public class Program
{
    static void Main(string[] args)
    {
        HelicopterGame game = new HelicopterGame();
        game.Start();
    }
}

public class HelicopterGame
{
    private const int Width = 30;
    private const int Height = 10;
    private bool isGameOver = false;
    private int helicopterPosition;
    private Random random = new Random();
    private List<int> obstacles = new List<int>();
    private int score = 0;

    public HelicopterGame()
    {
        helicopterPosition = Height / 2; // Начальная позиция вертолета
        GenerateObstacles();
    }

    public void Start()
    {
        Console.CursorVisible = false;
        Console.Clear();
        while (!isGameOver)
        {
            Update();
            Draw();
            Thread.Sleep(200);
        }
        Console.SetCursorPosition(0, Height + 1);
        Console.WriteLine($"Игра окончена! Ваш счет: {score}");
    }

    private void GenerateObstacles()
    {
        for (int i = 0; i < Height; i++)
        {
            if (random.Next(0, 3) == 0) // 1 из 3 шансов на создание препятствия
            {
                obstacles.Add(i);
            }
        }
    }

    private void Update()
    {
        // Управление вертолетом
        if (Console.KeyAvailable)
        {
            ConsoleKeyInfo keyInfo = Console.ReadKey(true);
            if (keyInfo.Key == ConsoleKey.UpArrow && helicopterPosition > 0)
            {
                helicopterPosition--;
            }
            else if (keyInfo.Key == ConsoleKey.DownArrow && helicopterPosition < Height - 1)
            {
                helicopterPosition++;
            }
        }

        // Изменять позицию препятствий
        obstacles.RemoveAll(x => x >= Width);
        if (random.Next(0, 10) == 0)
        {
            obstacles.Add(Width - 1);
        }

        // Проверка на столкновение
        foreach (var obstacle in obstacles)
        {
            if (obstacle == helicopterPosition)
            {
                isGameOver = true;
            }
        }

        score++; // Увеличение счета
    }

    private void Draw()
    {
        Console.Clear();
        for (int i = 0; i < Height; i++)
        {
            for (int j = 0; j < Width; j++)
            {
                if (j == 0)
                {
                    Console.Write("|");
                }
                
                if (j == 1 && i == helicopterPosition)
                {
                    Console.Write("H"); // Вертикальное положение вертолета
                }
                else if (obstacles.Contains(j))
                {
                    Console.Write("X"); // Препятствие
                }
                else
                {
                    Console.Write(" ");
                }
                
                if (j == Width - 1)
                {
                    Console.Write("|");
                }
            }
            Console.WriteLine();
        }
        Console.WriteLine($"Счет: {score}");
    }
}
