using System;
using System.Collections.Generic;
using System.Linq;

namespace PolymorphismGenericsDemo
{
    // Интерфейс, описывающий установочный контракт для компонентов
    public interface IInstallable
    {
        // Метод установки компонента в компьютер (может возвращать результат или сообщение)
        string Install();
    }

    // Абстрактный базовый класс для всех компонентов компьютера
    public abstract class Component
    {
        // Общие свойства для всех компонентов
        public string Model { get; set; }
        public string Manufacturer { get; set; }
        public decimal Price { get; set; }

        protected Component(string manufacturer, string model, decimal price)
        {
            Manufacturer = manufacturer;
            Model = model;
            Price = price;
        }

        // Виртуальный метод вывода информации о компоненте — может быть переопределён в потомках
        public virtual string GetInfo()
        {
            return $"Component: {Manufacturer} {Model}\nPrice: {Price:C}";
        }

        // Переопределяем ToString() для удобного представления информации
        public override string ToString()
        {
            return GetInfo();
        }

        // Базовая реализация Equals для корректной работы перегруженных операторов == и !=
        public override bool Equals(object obj)
        {
            if (obj is Component other)
            {
                // Сравниваем по типу, производителю и модели (и цене)
                return this.GetType() == other.GetType()
                       && Manufacturer == other.Manufacturer
                       && Model == other.Model
                       && Price == other.Price;
            }
            return false;
        }

        public override int GetHashCode()
        {
            // Простая комбинированная хеш-функция
            return HashCode.Combine(GetType(), Manufacturer, Model, Price);
        }

        // Перегрузка операторов сравнения
        public static bool operator ==(Component a, Component b)
        {
            if (ReferenceEquals(a, b)) return true;
            if (a is null || b is null) return false;
            return a.Equals(b);
        }

        public static bool operator !=(Component a, Component b)
        {
            return !(a == b);
        }
    }

    // CPU — конкретный компонент
    public class CPU : Component, IInstallable
    {
        public int Cores { get; set; }
        public double FrequencyGHz { get; set; } // тактовая частота

        public CPU(string manufacturer, string model, decimal price, int cores, double freq)
            : base(manufacturer, model, price)
        {
            Cores = cores;
            FrequencyGHz = freq;
        }

        // Перекрываем GetInfo для специфики CPU
        public override string GetInfo()
        {
            return $"CPU:\nManufacturer: {Manufacturer}\nModel: {Model}\nCores: {Cores}\nFrequency: {FrequencyGHz} GHz\nPrice: {Price:C}";
        }

        // Реализация интерфейса установки
        public string Install()
        {
            return $"Installing CPU {Manufacturer} {Model} ({Cores} cores @ {FrequencyGHz} GHz)... Done.";
        }

        // Перегружаем ToString для консистентности (необязательно, но удобно)
        public override string ToString() => GetInfo();
    }

    // RAM — оперативная память
    public class RAM : Component, IInstallable
    {
        public int CapacityGB { get; set; }
        public string Type { get; set; } // DDR4, DDR5 и т.д.

        public RAM(string manufacturer, string model, decimal price, int capacityGb, string type)
            : base(manufacturer, model, price)
        {
            CapacityGB = capacityGb;
            Type = type;
        }

        public override string GetInfo()
        {
            return $"RAM:\nManufacturer: {Manufacturer}\nModel: {Model}\nCapacity: {CapacityGB} GB\nType: {Type}\nPrice: {Price:C}";
        }

        public string Install()
        {
            return $"Installing RAM {CapacityGB}GB {Type} ({Manufacturer} {Model})... Done.";
        }

        public override string ToString() => GetInfo();
    }

    // GPU — видеокарта
    public class GPU : Component, IInstallable
    {
        public int MemoryGB { get; set; }
        public string Chipset { get; set; }

        public GPU(string manufacturer, string model, decimal price, int memoryGb, string chipset)
            : base(manufacturer, model, price)
        {
            MemoryGB = memoryGb;
            Chipset = chipset;
        }

        public override string GetInfo()
        {
            return $"GPU:\nManufacturer: {Manufacturer}\nModel: {Model}\nMemory: {MemoryGB} GB\nChipset: {Chipset}\nPrice: {Price:C}";
        }

        public string Install()
        {
            return $"Installing GPU {Manufacturer} {Model} ({MemoryGB}GB {Chipset})... Done.";
        }

        public override string ToString() => GetInfo();
    }

    // HDD — дополнительный компонент (упомянут в тексте задания)
    public class HDD : Component, IInstallable
    {
        public int CapacityGB { get; set; }
        public int RPM { get; set; }

        public HDD(string manufacturer, string model, decimal price, int capacityGb, int rpm)
            : base(manufacturer, model, price)
        {
            CapacityGB = capacityGb;
            RPM = rpm;
        }

        public override string GetInfo()
        {
            return $"HDD:\nManufacturer: {Manufacturer}\nModel: {Model}\nCapacity: {CapacityGB} GB\nRPM: {RPM}\nPrice: {Price:C}";
        }

        public string Install()
        {
            return $"Installing HDD {Manufacturer} {Model} ({CapacityGB}GB @ {RPM} RPM)... Done.";
        }

        public override string ToString() => GetInfo();
    }

    // Класс компьютера, содержащий коллекцию компонентов
    public class Computer
    {
        // Внутренний список компонентов
        private readonly List<Component> _components = new List<Component>();

        // Добавление компонента (Generic T: должен быть Component и IInstallable)
        public void AddComponent<T>(T component) where T : Component, IInstallable
        {
            if (component == null) throw new ArgumentNullException(nameof(component));
            _components.Add(component);
            // Выполняем установку (демонстрация работы интерфейса)
            Console.WriteLine(component.Install());
        }

        // Удаление компонента (по объекту)
        public bool RemoveComponent(Component component)
        {
            if (component == null) return false;
            bool removed = _components.Remove(component);
            if (removed)
            {
                Console.WriteLine($"Removed component: {component.Manufacturer} {component.Model}");
            }
            else
            {
                Console.WriteLine($"Component not found for removal: {component.Manufacturer} {component.Model}");
            }
            return removed;
        }

        // Удаление по типу и/или критериям (пример)
        public bool RemoveComponentByType<T>() where T : Component
        {
            var found = _components.OfType<T>().FirstOrDefault();
            if (found != null)
            {
                _components.Remove(found);
                Console.WriteLine($"Removed component by type: {found.GetType().Name} - {found.Manufacturer} {found.Model}");
                return true;
            }
            Console.WriteLine($"No component of type {typeof(T).Name} found to remove.");
            return false;
        }

        // Общая стоимость всех компонентов
        public decimal GetTotalPrice()
        {
            return _components.Sum(c => c.Price);
        }

        // Получить аккуратный построчный список компонентов
        public void PrintComponentsInfo()
        {
            Console.WriteLine("=== Computer Components ===");
            if (!_components.Any())
            {
                Console.WriteLine("No components installed.");
                return;
            }

            int index = 1;
            foreach (var c in _components)
            {
                Console.WriteLine($"Component #{index}:");
                // Каждый компонент уже форматирует информацию построчно в GetInfo()
                Console.WriteLine(c.GetInfo());
                Console.WriteLine(); // пустая строка между компонентами
                index++;
            }

            Console.WriteLine($"Total price: {GetTotalPrice():C}");
            Console.WriteLine("===========================\n");
        }
    }

    // Статическая фабрика для создания конфигураций компьютеров
    public static class ComputerFactory
    {
        // Базовая (office) конфигурация
        public static Computer CreateOfficePC()
        {
            var pc = new Computer();
            pc.AddComponent(new CPU("Intel", "i3-12100", 120m, 4, 3.3));
            pc.AddComponent(new RAM("Kingston", "ValueRAM", 40m, 8, "DDR4"));
            pc.AddComponent(new HDD("Seagate", "Barracuda", 50m, 1000, 7200));
            return pc;
        }

        // Игровая конфигурация
        public static Computer CreateGamingPC()
        {
            var pc = new Computer();
            pc.AddComponent(new CPU("AMD", "Ryzen 7 5800X", 320m, 8, 3.8));
            pc.AddComponent(new RAM("Corsair", "Vengeance 2x8", 90m, 16, "DDR4"));
            pc.AddComponent(new GPU("NVIDIA", "RTX 4070", 600m, 12, "Ada Lovelace"));
            pc.AddComponent(new HDD("WD", "Black", 120m, 2000, 7200));
            return pc;
        }

        // Конфигурация для workstation (профессиональная)
        public static Computer CreateWorkstation()
        {
            var pc = new Computer();
            pc.AddComponent(new CPU("Intel", "Xeon W-2295", 1200m, 18, 3.0));
            pc.AddComponent(new RAM("Samsung", "ECC-Registered", 400m, 64, "DDR4-ECC"));
            pc.AddComponent(new GPU("NVIDIA", "Quadro RTX 4000", 900m, 8, "Turing"));
            return pc;
        }
    }

    // Точка входа и демонстрация всех возможностей
    internal class Program
    {
        private static void Main(string[] args)
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8; // чтобы корректно отображать валюту и символы

            Console.WriteLine("=== Demo: Полиморфизм и Generic классы (Computer Components) ===\n");

            // Создаём пустой компьютер и добавляем компоненты вручную
            var myComputer = new Computer();

            var cpu = new CPU("AMD", "Ryzen 5 5600X", 200m, 6, 3.7);
            var ram = new RAM("GSkill", "Ripjaws 2x8", 85m, 16, "DDR4");
            var gpu = new GPU("NVIDIA", "GTX 1660", 220m, 6, "Turing");
            var hdd = new HDD("Toshiba", "P300", 55m, 1000, 7200);

            // Добавление через Generic-метод — демонстрация гибкости
            myComputer.AddComponent(cpu);
            myComputer.AddComponent(ram);
            myComputer.AddComponent(gpu);
            myComputer.AddComponent(hdd);

            Console.WriteLine(); // пустая строка для читабельности

            // Показать текущие компоненты (вызовет GetInfo() — демонстрация полиморфизма)
            myComputer.PrintComponentsInfo();

            // Демонстрация перегрузки == и !=
            var cpuClone = new CPU("AMD", "Ryzen 5 5600X", 200m, 6, 3.7);
            Console.WriteLine("Сравнение процессоров (cpu и cpuClone):");
            Console.WriteLine($"cpu == cpuClone ? { (cpu == cpuClone ? "Yes" : "No") }");
            Console.WriteLine($"cpu != cpuClone ? { (cpu != cpuClone ? "Yes" : "No") }");
            Console.WriteLine();

            // Демонстрация удаления компонента
            myComputer.RemoveComponentByType<GPU>(); // удалит первую найденную видеокарту
            Console.WriteLine();

            // Печать после удаления
            myComputer.PrintComponentsInfo();

            // Демонстрация фабрики
            Console.WriteLine("Создаём готовые конфигурации через ComputerFactory:\n");

            Console.WriteLine("Office PC:");
            var office = ComputerFactory.CreateOfficePC();
            office.PrintComponentsInfo();

            Console.WriteLine("Gaming PC:");
            var gaming = ComputerFactory.CreateGamingPC();
            gaming.PrintComponentsInfo();

            Console.WriteLine("Workstation:");
            var workstation = ComputerFactory.CreateWorkstation();
            workstation.PrintComponentsInfo();

            // Демонстрация сравнения разных типов компонентов (должно быть false)
            Console.WriteLine($"cpu equals gpu? { (cpu.Equals(gpu) ? "Yes" : "No") }");

            // В конце — суммарная цена example
            Console.WriteLine($"Price of myComputer components: {myComputer.GetTotalPrice():C}\n");

            Console.WriteLine("=== End of demo ===");
            // Не закрываем консоль мгновенно в случае запуска вне отладчика
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
