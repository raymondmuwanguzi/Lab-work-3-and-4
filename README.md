# Lab-work-3-and-4
none
#include <iostream>
#include <vector>
#include <iomanip>
#include <string>

using namespace std;

// ======================= DATE CLASS =======================
class date {
private:
    int day, month, year;

public:
    date(int d = 1, int m = 1, int y = 2000)
        : day(d), month(m), year(y) {}

    friend istream& operator>>(istream& is, date& d) {
        cout << "Enter date (day month year): ";
        is >> d.day >> d.month >> d.year;
        return is;
    }

    friend ostream& operator<<(ostream& os, const date& d) {
        os << d.day << "/" << d.month << "/" << d.year;
        return os;
    }
};

// ======================= MAN CLASS =======================
class man {
private:
    string firstName;
    string lastName;

public:
    man(string f = "", string l = "")
        : firstName(f), lastName(l) {}

    friend istream& operator>>(istream& is, man& m) {
        cout << "First name: ";
        is >> m.firstName;
        cout << "Last name: ";
        is >> m.lastName;
        return is;
    }

    friend ostream& operator<<(ostream& os, const man& m) {
        os << m.firstName << " " << m.lastName;
        return os;
    }

    bool operator==(const man& other) const {
        return firstName == other.firstName &&
               lastName == other.lastName;
    }
};

// ======================= WORKER (LAB 3 BASE DATA) =======================
class worker {
protected:
    string name;
    string position;
    double salary;

public:
    worker(string n = "", string p = "", double s = 0)
        : name(n), position(p), salary(s) {}

    virtual ~worker() {}

    virtual void input() {
        cout << "Enter name: ";
        cin >> name;
        cout << "Enter position: ";
        cin >> position;
        cout << "Enter salary: ";
        cin >> salary;
    }

    virtual void display() const {
        cout << left << setw(15) << name
             << setw(15) << position
             << setw(10) << salary;
    }
};

// ======================= WORKER1 =======================
class worker1 {
protected:
    man person;
    string profession;
    date employmentDate;

public:
    worker1() {}

    virtual void input() {
        cin >> person;
        cout << "Profession: ";
        cin >> profession;
        cin >> employmentDate;
    }

    virtual void display() const {
        cout << left << setw(20) << person
             << setw(15) << profession
             << setw(15) << employmentDate;
    }

    const man& getMan() const {
        return person;
    }
};

// ======================= BASE CLASS =======================
class common {
public:
    common() {
        cout << "Common constructor\n";
    }

    virtual ~common() {
        cout << "Common destructor\n";
    }

    virtual void input() = 0;
    virtual void display() const = 0;
    virtual bool equals(const man& m) const = 0;
};

// ======================= DERIVED CLASS: STAFF =======================
class staff : public common, public worker {
private:
    man person;

public:
    staff() {
        cout << "Staff constructor\n";
    }

    ~staff() {
        cout << "Staff destructor\n";
    }

    void input() override {
        cout << "\n--- STAFF INPUT ---\n";
        cin >> person;
        worker::input();
    }

    void display() const override {
        cout << left << setw(20) << person;
        worker::display();
        cout << endl;
    }

    bool equals(const man& m) const override {
        return person == m;
    }

    friend istream& operator>>(istream& is, staff& s) {
        s.input();
        return is;
    }

    friend ostream& operator<<(ostream& os, const staff& s) {
        s.display();
        return os;
    }
};

// ======================= DERIVED CLASS: DEPARTMENT =======================
class department : public common, public worker1 {
private:
    string departmentName;

public:
    department() {
        cout << "Department constructor\n";
    }

    ~department() {
        cout << "Department destructor\n";
    }

    void input() override {
        cout << "\n--- DEPARTMENT INPUT ---\n";
        worker1::input();
        cout << "Department name: ";
        cin >> departmentName;
    }

    void display() const override {
        worker1::display();
        cout << setw(15) << departmentName << endl;
    }

    bool equals(const man& m) const override {
        return getMan() == m;
    }

    friend istream& operator>>(istream& is, department& d) {
        d.input();
        return is;
    }

    friend ostream& operator<<(ostream& os, const department& d) {
        d.display();
        return os;
    }
};

// ======================= MAIN =======================
int main() {
    int n1, n2;

    cout << "Enter number of STAFF: ";
    cin >> n1;
    staff* staffArr = new staff[n1];

    for (int i = 0; i < n1; i++)
        cin >> staffArr[i];

    cout << "\nEnter number of DEPARTMENTS: ";
    cin >> n2;
    department* depArr = new department[n2];

    for (int i = 0; i < n2; i++)
        cin >> depArr[i];

    // SEARCH
    man searchPerson;
    cout << "\n--- SEARCH PERSON ---\n";
    cin >> searchPerson;

    vector<common*> Result;

    for (int i = 0; i < n1; i++)
        if (staffArr[i].equals(searchPerson))
            Result.push_back(&staffArr[i]);

    for (int i = 0; i < n2; i++)
        if (depArr[i].equals(searchPerson))
            Result.push_back(&depArr[i]);

    // OUTPUT RESULTS
    cout << "\n--- SEARCH RESULTS ---\n";
    cout << left << setw(20) << "Name"
         << setw(15) << "Position/Prof"
         << setw(15) << "Extra"
         << endl;

    for (auto ptr : Result)
        ptr->display();  // 🔥 POLYMORPHISM

    delete[] staffArr;
    delete[] depArr;

    return 0;
}
