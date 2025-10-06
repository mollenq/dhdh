using System;
using System.Collections.Generic;

namespace StoreApp
{
    // 1. Класс Товар (Product)
    public class Product
    {
        private string name;
        private decimal price;
        private int quantity;
        protected string category;
        internal string description;

        public Product(string name, decimal price, int quantity, string category, string description)
        {
            this.name = name;
            this.price = price;
            this.quantity = quantity;
            this.category = category;
            this.description = description;
        }

        public decimal GetTotalPrice()
        {
            return price * quantity;
        }

        public void UpdateQuantity(int amount)
        {
            quantity += amount;
            if (quantity < 0) quantity = 0;
        }

        public string GetProductInfo()
        {
            return
                $"Название: {name}\n" +
                $"Категория: {category}\n" +
                $"Цена: {price} руб.\n" +
                $"Количество: {quantity}\n" +
                $"Описание: {description}\n" +
                $"Общая стоимость: {GetTotalPrice()} руб.\n";
        }

        public string Name => name;
        public decimal Price => price;
        public int Quantity => quantity;
    }

    // 2. Класс Продавец (Seller)
    public class Seller
    {
        private string name;
        private string employeeId;
        protected decimal salary;
        internal string contactInfo;
        private List<Product> products = new List<Product>();

        public Seller(string name, string employeeId, decimal salary, string contactInfo)
        {
            this.name = name;
            this.employeeId = employeeId;
            this.salary = salary;
            this.contactInfo = contactInfo;
        }

        public void AddProduct(Product product)
        {
            products.Add(product);
        }

        public void SellProduct(Product product, int quantity)
        {
            if (products.Contains(product))
            {
                product.UpdateQuantity(-quantity);
                Console.WriteLine($"Продавец {name} продал {quantity} ед. товара \"{product.Name}\"\n");
            }
            else
            {
                Console.WriteLine("❌ Товар не найден у продавца!\n");
            }
        }

        public string GetSellerInfo()
        {
            return
                $"Имя: {name}\n" +
                $"ID: {employeeId}\n" +
                $"Зарплата: {salary} руб.\n" +
                $"Контактная информация: {contactInfo}\n";
        }

        public List<Product> GetProducts()
        {
            return products;
        }

        public string Name => name;
    }

    // 3. Класс Магазин (Store)
    public class Store
    {
        private string storeName;
        private string location;
        public string storeHours;

        private List<Seller> sellers = new List<Seller>();

        public Store(string storeName, string location, string storeHours)
        {
            this.storeName = storeName;
            this.location = location;
            this.storeHours = storeHours;
        }

        public void AddSeller(Seller seller)
        {
            sellers.Add(seller);
        }

        public void ListProducts()
        {
            Console.WriteLine($"\n=== Товары в магазине: {storeName} ===\n");
            foreach (var seller in sellers)
            {
                Console.WriteLine($"Продавец: {seller.Name}");
                Console.WriteLine("------------------------------------");
                foreach (var product in seller.GetProducts())
                {
                    Console.WriteLine(product.GetProductInfo());
                }
            }
        }

        public string GetStoreInfo()
        {
            return
                $"Название магазина: {storeName}\n" +
                $"Адрес: {location}\n" +
                $"Время работы: {storeHours}\n";
        }
    }

    // Тестирование
    class Program
    {
        static void Main()
        {
            Product phone = new Product("Смартфон", 50000, 10, "Электроника", "Флагманский телефон");
            Product laptop = new Product("Ноутбук", 90000, 5, "Электроника", "Игровой ноутбук");

            Seller seller = new Seller("Иван Петров", "S001", 60000, "ivan@sales.com");
            seller.AddProduct(phone);
            seller.AddProduct(laptop);

            Store store = new Store("ТехноМаркет", "ул. Центральная, 12", "9:00 - 21:00");
            store.AddSeller(seller);

            Console.WriteLine("=== Информация о магазине ===");
            Console.WriteLine(store.GetStoreInfo());

            Console.WriteLine("=== Информация о продавце ===");
            Console.WriteLine(seller.GetSellerInfo());

            store.ListProducts();

            seller.SellProduct(phone, 3);

            Console.WriteLine("\n=== После продажи ===");
            store.ListProducts();
        }
    }
}
