using System;
using System.Collections.Generic;

enum TaskStatus { ToDo, InProgress, Completed }

class Task
{
    public int ID { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public DateTime Date { get; set; }
    public TaskStatus Status { get; set; } = TaskStatus.ToDo;

    public Task(int id, string name, string description, DateTime date)
    {
        ID = id;
        Name = name;
        Description = description;
        Date = date;
    }

    public override string ToString()
    {
        return $"[{Status}] {ID}: {Name} ({Date.ToShortDateString()}) - {Description}";
    }
}

class TaskManager
{
    private LinkedList<Task> toDoList = new LinkedList<Task>();
    private Stack<Task> inProgressStack = new Stack<Task>();
    private Queue<Task> completedQueue = new Queue<Task>();

   
    public void AddTask(Task task)
    {
        if (toDoList.Count == 0)
        {
            toDoList.AddFirst(task);
            return;
        }

        var current = toDoList.First;
        while (current != null && current.Value.Date <= task.Date)
        {
            current = current.Next;
        }

        if (current == null)
            toDoList.AddLast(task);
        else
            toDoList.AddBefore(current, task);
    }

  
    public void StartTask()
    {
        if (toDoList.Count == 0)
        {
            Console.WriteLine("No tasks in To-Do list.");
            return;
        }

        var task = toDoList.First.Value;
        toDoList.RemoveFirst();
        task.Status = TaskStatus.InProgress;
        inProgressStack.Push(task);
    }

 
    public void CompleteTask()
    {
        if (inProgressStack.Count == 0)
        {
            Console.WriteLine("No tasks in In-Progress list.");
            return;
        }

        var task = inProgressStack.Pop();
        task.Status = TaskStatus.Completed;
        completedQueue.Enqueue(task);
    }

    public void PrintAll()
    {
        Console.WriteLine("\n--- To-Do Tasks (Sorted by Date) ---");
        foreach (var task in toDoList)
            Console.WriteLine(task);

        Console.WriteLine("\n--- In-Progress Tasks (Stack) ---");
        foreach (var task in inProgressStack)
            Console.WriteLine(task);

        Console.WriteLine("\n--- Completed Tasks (Queue) ---");
        foreach (var task in completedQueue)
            Console.WriteLine(task);
    }
}


class Program
{
    static void Main()
    {
        TaskManager manager = new TaskManager();

        manager.AddTask(new Task(1, "Task A", "Do something important", DateTime.Parse("2025-06-01")));
        manager.AddTask(new Task(2, "Task B", "Another task", DateTime.Parse("2025-05-30")));
        manager.AddTask(new Task(3, "Task C", "Final task", DateTime.Parse("2025-06-02")));

        manager.PrintAll();

        manager.StartTask();  
        manager.StartTask();
        manager.CompleteTask();  
        manager.PrintAll();
    }
}
