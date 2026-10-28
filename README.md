## Практическая работа №11
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace TransportFleetApp
{
    // Шаг 1.
    public class Transport
    {
        protected string TransportId;
        protected int CurrentSpeed;
        private double _mileageKm;

        public string Id => TransportId;
        public double Mileage => _mileageKm;
        public int Speed { get => CurrentSpeed; protected set => CurrentSpeed = Math.Max(0, value); }

        public Transport(string id, double mileage)
        {
            TransportId = id;
            _mileageKm = Math.Max(0, mileage);
            CurrentSpeed = 0;
        }

        public virtual void Move(double distanceKm)
        {
            if (distanceKm <= 0) return;
            _mileageKm += distanceKm;
            Console.WriteLine($"Транспорт {Id} переместился на {distanceKm} км. Общий пробег: {_mileageKm} км.");
        }

        public virtual void DisplayInfo()
        {
            Console.WriteLine($"ID: {Id}, Скорость: {Speed} км/ч, Пробег: {Mileage} км");
        }
    }

    // Шаг 2.
    public class PublicBus : Transport
    {
        public int PassengerCapacity { get; }
        public int CurrentPassengers { get; private set; }

        public PublicBus(string id, double mileage, int passengerCapacity)
            : base(id, mileage)
        {
            PassengerCapacity = Math.Max(1, passengerCapacity);
            CurrentPassengers = 0;
        }

        public void BoardPassengers(int count)
        {
            if (count <= 0) return;
            int availableSeats = PassengerCapacity - CurrentPassengers;
            int actualBoarding = Math.Min(count, availableSeats);
            CurrentPassengers += actualBoarding;
            Console.WriteLine($"В автобус {Id} посажено {actualBoarding} пассажиров. В салоне: {CurrentPassengers}/{PassengerCapacity}.");
        }

        public override void Move(double distanceKm)
        {
            base.Move(distanceKm);
            Console.WriteLine("Автобус следует по городскому маршруту с остановками.");
        }
    }

    // Шаг 3.
    public sealed class ElectricScooter : Transport
    {
        private int _batteryPercent;
        public int BatteryPercent => _batteryPercent;

        public ElectricScooter(string id, double mileage, int batteryPercent)
            : base(id, mileage)
        {
            _batteryPercent = Math.Clamp(batteryPercent, 0, 100);
        }

        public void Recharge()
        {
            _batteryPercent = 100;
            Console.WriteLine($"Самокат {Id} заряжен до 100%.");
        }

        public override void Move(double distanceKm)
        {
            int requiredCharge = (int)(distanceKm * 2);
            if (_batteryPercent < requiredCharge)
            {
                Console.WriteLine($"Движение самоката {Id} заблокировано. Недостаточно заряда: {_batteryPercent}%. Требуется: {requiredCharge}%.");
                return;
            }

            _batteryPercent -= requiredCharge;
            base.Move(distanceKm);
            Console.WriteLine($"Электросамокат {Id} проехал {distanceKm} км. Остаток заряда: {_batteryPercent}%.");
        }
    }

    // Шаг 4.
    public class CargoTruck : Transport
    {
        public double MaxCargoTons { get; }
        public bool HasTrailer { get; set; }

        public CargoTruck(string id, double mileage, double maxCargoTons, bool hasTrailer)
            : base(id, mileage)
        {
            MaxCargoTons = Math.Max(0, maxCargoTons);
            HasTrailer = hasTrailer;
        }

        public override void DisplayInfo()
        {
            base.DisplayInfo();
            Console.WriteLine($"Грузоподъемность: {MaxCargoTons} тонн. Прицеп: {(HasTrailer ? "Присоединен" : "Отсутствует")}.");
        }

        public void UnloadCargo()
        {
            Console.WriteLine($"Грузовик {Id}: кузов разгружен. Груз доставлен.");
        }
    }

    // Шаг 5.
    internal class Program
    {
        static void Main(string[] args)
        {
            Transport[] fleet = new Transport[4];
            fleet[0] = new PublicBus("PB-001", 15000.5, 50);
            fleet[1] = new ElectricScooter("ES-100", 250.0, 15); // Заряд ниже 20%
            fleet[2] = new ElectricScooter("ES-101", 500.0, 85); // Заряд выше 20%
            fleet[3] = new CargoTruck("CT-500", 80000.0, 20.0, true);

            Console.WriteLine("=== Общий выезд автопарка на 10 км ===");
            foreach (var item in fleet)
            {
                item.Move(10.0);
            }

            Console.WriteLine("\n=== Проверка заряда самокатов (оператор is) ===");
            foreach (var item in fleet)
            {
                if (item is ElectricScooter scooter)
                {
                    Console.WriteLine($"Проверка самоката {scooter.Id}: текущий заряд {scooter.BatteryPercent}%.");
                    if (scooter.BatteryPercent < 20)
                    {
                        scooter.Recharge();
                    }
                }
            }

            Console.WriteLine("\n=== Разгрузка грузовиков (оператор as) ===");
            foreach (var item in fleet)
            {
                CargoTruck truck = item as CargoTruck;
                if (truck != null)
                {
                    truck.UnloadCargo();
                }
            }

            Console.WriteLine("\n=== Итоговая информация по автопарку ===");
            foreach (var item in fleet)
            {
                item.DisplayInfo();
                Console.WriteLine(new string('-', 30));
            }
        }
    }
}
```
### Результат выполнения:
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7d7df809-07ab-4864-ad13-f64d0855d1eb" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bab3942c-0379-4825-a4f7-35f2230e4dc3" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cead0061-7ac9-40b3-a1a5-4cd1c19638cd" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/91ede141-254d-4082-8dc2-5f80cf77b9a7" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e0f259c2-027b-4383-895f-bf1e8867342a" />
// Делали Аристов и Башкатова
