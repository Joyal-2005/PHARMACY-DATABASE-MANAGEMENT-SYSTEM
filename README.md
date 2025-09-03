
CREATE DATABASE PharmacyDB;
USE PharmacyDB;
CREATE TABLE Medicine (
    medicine_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    brand VARCHAR(100),
    type VARCHAR(50), -- Tablet, Syrup, Injection etc.
    quantity INT NOT NULL CHECK (quantity >= 0),
    unit_price DECIMAL(10,2) NOT NULL,
    expiry_date DATE NOT NULL
);


CREATE TABLE Supplier (
    supplier_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    contact_number VARCHAR(15),
    email VARCHAR(100),
    address TEXT
);
CREATE TABLE Customer (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    contact_number VARCHAR(15),
    email VARCHAR(100),
    address TEXT
);

CREATE TABLE Purchase (
    purchase_id INT AUTO_INCREMENT PRIMARY KEY,
    supplier_id INT NOT NULL,
    medicine_id INT NOT NULL,
    purchase_date DATE DEFAULT CURRENT_DATE,
    quantity INT NOT NULL CHECK (quantity > 0),
    purchase_price DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (supplier_id) REFERENCES Supplier(supplier_id) ON DELETE CASCADE,
    FOREIGN KEY (medicine_id) REFERENCES Medicine(medicine_id) ON DELETE CASCADE
);

CREATE TABLE Sales (
    sale_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    medicine_id INT NOT NULL,
    sale_date DATE DEFAULT CURRENT_DATE,
    quantity INT NOT NULL CHECK (quantity > 0),
    sale_price DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES Customer(customer_id) ON DELETE CASCADE,
    FOREIGN KEY (medicine_id) REFERENCES Medicine(medicine_id) ON DELETE CASCADE
);


CREATE TRIGGER reduce_stock_after_sale
AFTER INSERT ON Sales
FOR EACH ROW
BEGIN
    UPDATE Medicine
    SET quantity = quantity - NEW.quantity
    WHERE medicine_id = NEW.medicine_id;
END;

CREATE TRIGGER increase_stock_after_purchase
AFTER INSERT ON Purchase
FOR EACH ROW
BEGIN
    UPDATE Medicine
    SET quantity = quantity + NEW.quantity
    WHERE medicine_id = NEW.medicine_id;
END;

