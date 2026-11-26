class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price

    def display_info(self):
        return f"Product Name: {self.name}, Price: ₱{self.price:.2f}"


class ProductManager:
    def __init__(self):
        self.products = []

    def add_product(self, product):
        self.products.append(product)

    def display_all_products(self):
        print("=" * 36)
        print("             PRODUCT MENU")
        print("=" * 36)
        for number, product in enumerate(self.products, start=1):
            print(f"{number}. {product.name:<20} ₱{product.price:>7.2f}")
        print("=" * 36)


class Receipt:

    def __init__(self, products):
        self.products = products

    def calculate_subtotal(self):
        return sum(product.price for product in self.products)

    def calculate_total_quantity(self):
        return len(self.products)

    def display_receipt(self):
        print("=" * 36)
        print("           ORDER RECEIPT")
        print("=" * 36)

        quantities = {}
        for product in self.products:
            if product.name in quantities:
                quantities[product.name]["quantity"] += 1
            else:
                quantities[product.name] = {
                    "product": product,
                    "quantity": 1,
                }

        print("AMOUNT  PRODUCT")
        print("-" * 36)
        for item in quantities.values():
            product = item["product"]
            quantity = item["quantity"]
            line_total = product.price * quantity
            print(f"{quantity:<7} {product.name:<18} ₱{line_total:>7.2f}")

        subtotal = self.calculate_subtotal()
        print("-" * 36)
        print(f"PRODUCTS: {self.calculate_total_quantity()}")
        print(f"{'SUBTOTAL':<26} ₱{subtotal:>7.2f}")
        print(f"{'AMOUNT TO PAY':<26} ₱{subtotal:>7.2f}")
        print("=" * 36)
        print("Thank you for your order!")


class Order:
    def __init__(self, product_manager):
        self.product_manager = product_manager
        self.selected_products = []

    def add_product(self, product, quantity=1):
        if quantity < 1:
            raise ValueError("Quantity must be at least 1")

        for _ in range(quantity):
            self.selected_products.append(product)

    def make_receipt(self):
        return Receipt(self.selected_products)


def create_store():
    manager = ProductManager()
    manager.add_product(Product("Burger", 50.00))
    manager.add_product(Product("Fries", 35.00))
    manager.add_product(Product("Softdrink", 25.00))
    return manager


def take_order(manager):
    order = Order(manager)

    print()
    manager.display_all_products()

    while True:
        choices = input(
            "Enter menu number(s) separated by commas (or 0 to finish): "
        ).strip()

        if choices == "0":
            break

        try:
            choice_numbers = [int(choice.strip()) for choice in choices.split(",")]

            if any(number < 1 or number > len(manager.products)
                   for number in choice_numbers):
                print("Please choose valid menu number(s).")
                continue

            for choice_number in choice_numbers:
                product = manager.products[choice_number - 1]
                quantity = int(input(f"Enter quantity for {product.name}: ").strip())

                if quantity < 1:
                    print(f"Quantity for {product.name} must be at least 1.")
                    continue

                order.add_product(product, quantity)
                print(f"Added {quantity} {product.name}(s).")

            print(f"Products in this order: {order.make_receipt().calculate_total_quantity()}")
        except ValueError:
            print("Please enter valid numbers.")

    return order


def main():
    manager = create_store()

    print("Welcome to the Burger Shop!")
    order = take_order(manager)

    if not order.selected_products:
        print("No items were ordered.")
        return

    print()
    order.make_receipt().display_receipt()


if __name__ == "__main__":
    main()

