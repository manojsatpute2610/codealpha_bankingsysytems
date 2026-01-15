#task_4_bankingsystems
# codealpha_bankingsysytems
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class Transaction {
public:
    string type;
    double amount;
    string details;
    Transaction(string t, double a, string d) : type(t), amount(a), details(d) {}
    void show() { cout << type << ": " << amount << " [" << details << "]\n"; }
};

class Account {
private:
    double balance;
public:
    string accNumber;
    vector<Transaction> history;

    Account(string acc, double b = 0) : accNumber(acc), balance(b) {}

    double getBalance() { return balance; }

    void deposit(double amount) {
        balance += amount;
        history.push_back(Transaction("Deposit", amount, "Self deposit"));
    }

    void withdraw(double amount) {
        if (amount > balance) { cout << "Insufficient funds.\n"; return; }
        balance -= amount;
        history.push_back(Transaction("Withdrawal", amount, "Self withdrawal"));
    }

    void transfer(Account &to, double amount) {
        if (amount > balance) { cout << "Insufficient funds.\n"; return; }
        balance -= amount;
        to.balance += amount;
        history.push_back(Transaction("Transfer", amount, "To " + to.accNumber));
        to.history.push_back(Transaction("Transfer", amount, "From " + accNumber));
    }

    void showTransactions() {
        cout << "Transaction History for " << accNumber << ":\n";
        if(history.empty()) cout << "No transactions yet.\n";
        for (auto &t : history) t.show();
    }

    void showInfo() {
        cout << "Account: " << accNumber << ", Balance: " << balance << "\n";
        showTransactions();
    }
};

class Customer {
public:
    string name;
    string username;
    string password;
    vector<Account> accounts;

    Customer(string n, string u, string p) : name(n), username(u), password(p) {}

    void addAccount(Account acc) { accounts.push_back(acc); }

    Account* getAccount(string accNum) {
        for (auto &a : accounts)
            if (a.accNumber == accNum) return &a;
        return nullptr;
    }

    void showAccounts() {
        cout << "Accounts for " << name << ":\n";
        for (auto &a : accounts) cout << "- " << a.accNumber << ", Balance: " << a.getBalance() << "\n";
    }
};

int main() {
    vector<Customer> customers;
    customers.push_back(Customer("Alice", "alice01", "pass123"));
    customers.back().addAccount(Account("A1001", 500));
    customers.back().addAccount(Account("A1002", 1000));

    customers.push_back(Customer("Bob", "bob02", "pass456"));
    customers.back().addAccount(Account("B2001", 800));

    // Example operations
    Customer &c1 = customers[0];
    Customer &c2 = customers[1];

    Account *acc1 = c1.getAccount("A1001");
    Account *acc2 = c2.getAccount("B2001");

    if(acc1 && acc2){
        acc1->deposit(200);
        acc1->withdraw(100);
        acc1->transfer(*acc2, 150);
    }

    c1.showAccounts();
    c2.showAccounts();

    cout << "\nDetailed Transactions:\n";
    acc1->showInfo();
    acc2->showInfo();

    return 0;
}
