# Adapter & Facade

# Structure Design

Structural design patterns help us organize and structure classes and objects in a way that is clean, maintainable, and extensible.

---

## 🧩 Adapter Design Pattern

### 🔧 Definition:

Adapter is a structural design pattern that **allows incompatible interfaces to work together**. It acts as a bridge between two classes that otherwise couldn’t communicate due to method or structure mismatch.

---

### 🧠 Real-World Analogy:

Imagine you bought an iPhone from the USA with a **US charging pin**, but in India, we use a different socket. The two are incompatible. So you use a **power adapter** to make the US pin compatible with Indian sockets.

This is exactly what the Adapter design pattern does in code — **it makes two incompatible classes compatible**.

---

### ⚠️ The Problem

Let’s say we have a fintech company like **PhonePe** that handles banking transactions.

Initially, PhonePe uses a third-party banking service — **YesBankService** — which has two methods:

```java
getBalance();
moneyTransfer();

```

So, the PhonePe class might directly use it like this:

```java
YesBankService yb = new YesBankService();
yb.getBalance();
yb.moneyTransfer();

```

This works, but now:

1. **We're tightly coupled to YesBankService.**
2. If YesBank goes down, PhonePe wants to switch to **ICICIBankService**.
3. ICICIBankService uses **different method names**:

```java
balanceCheck();
sendMoney();

```

1. We'd have to manually change every reference to `getBalance()` → `balanceCheck()` and `moneyTransfer()` → `sendMoney()` across the entire codebase.

This is:

- Time-consuming
- Error-prone
- Violates the **Dependency Inversion Principle**
- Makes testing and scaling harder

---

### ✅ The Solution: Adapter Pattern

Instead of using third-party services directly, we create a common **interface** that our application understands:

```java
interface BankApi {
    double checkBalance();
    void transferMoney();
}

```

Now we create **adapters** for each bank that implement this interface.

### 🛠 YesBank Adapter

```java
class YesBankService {
    double getBalance() { ... }
    void moneyTransfer() { ... }
}

class YesBankAdapter implements BankApi {
    YesBankService yesBank = new YesBankService();

    public double checkBalance() {
        return yesBank.getBalance();
    }

    public void transferMoney() {
        yesBank.moneyTransfer();
    }
}

```

### 🛠 ICICI Bank Adapter

```java
class ICICIBankService {
    double balanceCheck() { ... }
    void sendMoney() { ... }
}

class ICICIBankAdapter implements BankApi {
    ICICIBankService icici = new ICICIBankService();

    public double checkBalance() {
        return icici.balanceCheck();
    }

    public void transferMoney() {
        icici.sendMoney();
    }
}

```

---

### 🧠 Behind the Scenes:

- The Adapter class acts as a **translator**.
- Client code stays unaware of third-party API method names.
- You only change the **adapter implementation**, not the client.
- Fixes **Dependency Inversion** — high-level modules (like PhonePe) now depend on abstraction (BankApi) not on concrete implementations.
- Future-proof: If another bank comes, we just write another adapter.

---

### 🏭 Adapter Factory: Runtime Switching

We can even choose which adapter to use **at runtime**:

```java
class AdapterFactory {
    public static BankApi getAdapter(String type) {
        if (type.equals("YES")) return new YesBankAdapter();
        else if (type.equals("ICICI")) return new ICICIBankAdapter();
        else throw new RuntimeException("Unsupported bank");
    }
}

String bankName = getUserSelectedBank();
BankApi bank = AdapterFactory.getAdapter(bankName);
bank.checkBalance();
bank.transferMoney();

```

This gives us **pluggability**, **low coupling**, and **clean abstraction**.

---

## 🌉 Facade Design Pattern

### 🔧 Definition:

Facade is a structural design pattern that **provides a simplified interface to a complex subsystem**. It hides the internal complexity and exposes a cleaner, higher-level API for clients.

---

### 🧠 Real-World Analogy:

 let's take a real-life UPI payment use case in PhonePe.

Imagine you want to do a UPI payment. Under the hood, these things happen:

1. Validate UPI ID
2. Check if user has funds
3. Generate and verify OTP
4. Transfer money
5. Send confirmation

Each of these steps is done by a different system.

---

### ⚠️ Problem Without Facade:

Your client would have to coordinate all those subsystems directly:

```java
if (validator.isValid(upiId)) {
    if (bank.hasSufficientFunds(upiId)) {
        String otp = otpService.generate();
        if (otpService.verify(otp)) {
            bank.transfer(upiId, amount);
            notify.sendSuccess();
        }
    }
}

```

That's too much burden on the client!

---

### ✅ Solution With Facade:

Let’s create a facade to abstract this flow.

```java
class UpiPaymentFacade {
    private Validator validator = new Validator();
    private BankService bank = new BankService();
    private OtpService otp = new OtpService();
    private NotificationService notify = new NotificationService();

    public void makeUpiPayment(String upiId, double amount) {
        if (validator.isValid(upiId) && bank.hasSufficientFunds(upiId, amount)) {
            String otpCode = otp.generateOTP();
            if (otp.verify(otpCode)) {
                bank.transfer(upiId, amount);
                notify.sendSuccessMessage();
            }
        }
    }
}

```

Now the client code becomes very simple:

```java
UpiPaymentFacade facade = new UpiPaymentFacade();
facade.makeUpiPayment("ramesh@ybl", 1000);

```

---

### 🧠 Behind the Scenes:

- The client no longer knows about the underlying complexity.
- High-level logic is clear, centralized, and easier to maintain.
- Subsystems can change independently — only the facade knows how to use them.
- Improves **readability**, **testability**, and **flexibility**.

---

## ✅ Summary

| Pattern | Purpose | When to Use |
| --- | --- | --- |
| Adapter | Bridge incompatible interfaces | When you want to reuse existing code without modifying it |
| Facade | Hide complexity behind a simple interface | When dealing with a complex subsystem you want to simplify |

---

