using System;

public interface IDeliverable
{
    bool RequiresSpecialDocking { get; }
    void LoadCargo(int weight);
    void UnloadCargo(int weight);
}

public class DeliveryTruck : IDeliverable
{
    private string truckName;
    private int currentLoadWeight;
    private int maxLoadCapacity;

    public DeliveryTruck(string name, int capacity)
    {
        truckName = name;
        maxLoadCapacity = capacity;
        currentLoadWeight = 0;
    }

    public string TruckName => truckName;

    public int CurrentLoadWeight
    {
        get { return currentLoadWeight; }
        set
        {
            if (value < 0)
            {
                currentLoadWeight = 0;
                Console.WriteLine("Warning: Load weight cannot be negative. Set to 0.");
            }
            else if (value > maxLoadCapacity)
            {
                currentLoadWeight = maxLoadCapacity;
                Console.WriteLine("Warning: Load weight exceeds capacity. Set to max capacity.");
            }
            else
            {
                currentLoadWeight = value;
            }
        }
    }

    public virtual void StartEngine()
    {
        Console.WriteLine("DeliveryTruck engine started.");
    }

    bool IDeliverable.RequiresSpecialDocking => false;

    public void LoadCargo(int weight)
    {
        CurrentLoadWeight += weight;
        Console.WriteLine("Cargo was loaded.");
    }

    public void UnloadCargo(int weight)
    {
        CurrentLoadWeight -= weight;
        Console.WriteLine("Cargo was unloaded.");
    }
}

public class RefrigeratedTruck : DeliveryTruck
{
    public RefrigeratedTruck(string name, int capacity) : base(name, capacity)
    {
    }

    public override void StartEngine()
    {
        Console.WriteLine("Cooling system turning on.");
    }
}

public class LuxuryCourierVan : DeliveryTruck
{
    private bool hasPremiumInterior;

    public LuxuryCourierVan(string name, int capacity) : base(name, capacity)
    {
        hasPremiumInterior = true;
    }

    public override void StartEngine()
    {
        Console.WriteLine("Luxury van engine started smoothly.");
    }

    bool IDeliverable.RequiresSpecialDocking => true;

    public void ActivateClimateControl()
    {
        if (hasPremiumInterior)
        {
            Console.WriteLine("Climate control on.");
        }
        else
        {
            Console.WriteLine("Standard AC on.");
        }
    }
}

class Program
{
    static void Main()
    {
        DeliveryTruck truck1 = new DeliveryTruck("Standard Truck", 1000);
        RefrigeratedTruck truck2 = new RefrigeratedTruck("Cool Truck", 800);
        LuxuryCourierVan van = new LuxuryCourierVan("Lux Van", 500);

        Console.WriteLine("=== Engine Test ===");
        truck1.StartEngine();
        truck2.StartEngine();
        van.StartEngine();

        Console.WriteLine("\n=== Safety Test ===");
        truck1.LoadCargo(1500);
        truck1.UnloadCargo(2000);

        Console.WriteLine("\n=== Fleet Test ===");
        IDeliverable[] myFleet = { truck1, truck2, van };
        foreach (var vehicle in myFleet)
        {
            vehicle.LoadCargo(50);
            Console.WriteLine($"RequiresSpecialDocking: {((IDeliverable)vehicle).RequiresSpecialDocking}");
        }
    }
}
