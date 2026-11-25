# Bank-Management-System
Bank management System C++ Program

**C++ Code**

```
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <sstream>
#include <iomanip>
#include <algorithm>
#include <cctype>
#include <limits>

using namespace std;

// DigiCode Byte Bank
const string FILE_NAME = "employees.txt";

// Employee structure
struct Employee {
    string id;
    string name;
    string fatherName;
    string department;
    string designation;
    string category;

    double basicPay;
    double medicalAllowance;
    double travelAllowance;
    double otherAllowance;

    string loanType;
    double loanAmount;
    double loanInstallment;

    string insurance;
    double insurancePremium;

    double grossSalary() const {
        return basicPay + medicalAllowance
             + travelAllowance + otherAllowance;
    }

    double totalDeductions() const {
        return loanInstallment + insurancePremium;
    }

    double netSalary() const {
        return grossSalary() - totalDeductions();
    }
};

vector<Employee> employees;

// Input a text value
string getText(string message) {
    string value;

    while (true) {
        cout << message;
        getline(cin, value);

        if (value.empty()) {
            cout << "This field cannot be empty!\n";
        }
        else if (value.find('|') != string::npos) {
            cout << "The character | is not allowed!\n";
        }
        else {
            return value;
        }
    }
}

// Input a non-negative number
double getAmount(string message) {
    double value;
    string line;

    while (true) {
        cout << message;
        getline(cin, line);

        stringstream ss(line);

        if (ss >> value &&
            !(ss >> ws && !ss.eof()) &&
            value >= 0) {
            return value;
        }

        cout << "Please enter a valid non-negative amount!\n";
    }
}

// Validate employee ID
bool validID(string id, string department) {

    if (id.size() < 5 || id.size() > 6)
        return false;

    int prefixLength = static_cast<int>(id.size()) - 3;

    if (department.size() != prefixLength)
        return false;

    for (int i = 0; i < prefixLength; i++) {
        if (!isupper(static_cast<unsigned char>(id[i])) ||
            !isupper(static_cast<unsigned char>(department[i])) ||
            id[i] != department[i]) {
            return false;
        }
    }

    for (int i = prefixLength; i < static_cast<int>(id.size()); i++) {
        if (!isdigit(static_cast<unsigned char>(id[i])))
            return false;
    }

    return true;
}

// Check duplicate ID
bool idExists(string id) {
    for (const auto& e : employees) {
        if (e.id == id)
            return true;
    }
    return false;
}

// Save all employee records
void saveData() {
    ofstream file(FILE_NAME);

    if (!file) {
        cout << "Error saving employee data!\n";
        return;
    }

    for (const auto& e : employees) {
        file << e.id << "|"
             << e.name << "|"
             << e.fatherName << "|"
             << e.department << "|"
             << e.designation << "|"
             << e.category << "|"
             << e.basicPay << "|"
             << e.medicalAllowance << "|"
             << e.travelAllowance << "|"
             << e.otherAllowance << "|"
             << e.loanType << "|"
             << e.loanAmount << "|"
             << e.loanInstallment << "|"
             << e.insurance << "|"
             << e.insurancePremium << "\n";
    }
}

// Load employee records
void loadData() {
    ifstream file(FILE_NAME);
    string line;

    while (getline(file, line)) {
        stringstream ss(line);
        vector<string> fields;
        string field;

        while (getline(ss, field, '|')) {
            fields.push_back(field);
        }

        if (fields.size() != 15)
            continue;

        try {
            Employee e;

            e.id = fields[0];
            e.name = fields[1];
            e.fatherName = fields[2];
            e.department = fields[3];
            e.designation = fields[4];
            e.category = fields[5];

            e.basicPay = stod(fields[6]);
            e.medicalAllowance = stod(fields[7]);
            e.travelAllowance = stod(fields[8]);
            e.otherAllowance = stod(fields[9]);

            e.loanType = fields[10];
            e.loanAmount = stod(fields[11]);
            e.loanInstallment = stod(fields[12]);

            e.insurance = fields[13];
            e.insurancePremium = stod(fields[14]);

            if (validID(e.id, e.department) &&
                e.basicPay >= 0 &&
                e.medicalAllowance >= 0 &&
                e.travelAllowance >= 0 &&
                e.otherAllowance >= 0 &&
                e.loanAmount >= 0 &&
                e.loanInstallment >= 0 &&
                e.insurancePremium >= 0 &&
                e.loanInstallment <= e.loanAmount &&
                e.loanInstallment + e.insurancePremium <= e.grossSalary() &&
                !idExists(e.id)) {
                employees.push_back(e);
            }
        }
        catch (...) {
            continue;
        }
    }
}

// Display one employee
void displayEmployee(const Employee& e) {

    cout << "\n========================================\n";
    cout << "       DIGICODE BYTE BANK\n";
    cout << "          EMPLOYEE DETAILS\n";
    cout << "========================================\n";

    cout << fixed << setprecision(2);

    cout << "Employee ID       : " << e.id << "\n";
    cout << "Employee Name     : " << e.name << "\n";
    cout << "Father Name       : " << e.fatherName << "\n";
    cout << "Department        : " << e.department << "\n";
    cout << "Designation       : " << e.designation << "\n";
    cout << "Category          : " << e.category << "\n";

    cout << "\n--------- SALARY DETAILS ---------\n";

    cout << "Basic Pay         : Rs. " << e.basicPay << "\n";
    cout << "Medical Allowance : Rs. " << e.medicalAllowance << "\n";
    cout << "Travel Allowance  : Rs. " << e.travelAllowance << "\n";
    cout << "Other Allowance   : Rs. " << e.otherAllowance << "\n";

    cout << "Gross Salary      : Rs. " << e.grossSalary() << "\n";

    cout << "\n--------- LOAN DETAILS -----------\n";

    cout << "Loan Type         : " << e.loanType << "\n";
    cout << "Loan Amount       : Rs. " << e.loanAmount << "\n";
    cout << "Monthly Installment: Rs. "
         << e.loanInstallment << "\n";

    cout << "\n--------- INSURANCE DETAILS ------\n";

    cout << "Insurance         : " << e.insurance << "\n";
    cout << "Insurance Premium : Rs. "
         << e.insurancePremium << "\n";

    cout << "\n--------- MONTHLY PAYSLIP --------\n";

    cout << "Gross Salary      : Rs. " << e.grossSalary() << "\n";
    cout << "Total Deductions  : Rs. "
         << e.totalDeductions() << "\n";

    cout << "NET MONTHLY SALARY: Rs. "
         << e.netSalary() << "\n";

    cout << "========================================\n";
}

// Add employee
void addEmployee() {
    Employee e;

    cout << "\n===== ADD NEW EMPLOYEE =====\n";

    while (true) {
        e.department = getText("Department Code (GD, HR, IT): ");

        transform(e.department.begin(),
                  e.department.end(),
                  e.department.begin(),
                  [](unsigned char c) {
                      return toupper(c);
                  });

        if (e.department.size() < 2 ||
            e.department.size() > 3 ||
            !all_of(e.department.begin(),
                    e.department.end(),
                    [](unsigned char c) {
                        return isupper(c);
                    })) {
            cout << "Use a 2 or 3 letter department code!\n";
            continue;
        }

        e.id = getText("Employee ID (Example GD010): ");

        transform(e.id.begin(), e.id.end(), e.id.begin(),
                  [](unsigned char c) {
                      return toupper(c);
                  });

        if (!validID(e.id, e.department)) {
            cout << "Invalid ID! Department code must match "
                    "the ID prefix, followed by 3 digits.\n";
            continue;
        }

        if (idExists(e.id)) {
            cout << "This employee ID already exists!\n";
            continue;
        }

        break;
    }

    e.name = getText("Employee Name: ");
    e.fatherName = getText("Father Name: ");
    e.designation = getText("Designation: ");

    while (true) {
        e.category = getText(
            "Category (Senior/Intermediate/Junior): ");

        if (e.category == "Senior" ||
            e.category == "senior" ||
            e.category == "Intermediate" ||
            e.category == "intermediate" ||
            e.category == "Junior" ||
            e.category == "junior") {
            break;
        }

        cout << "Please enter Senior, Intermediate or Junior.\n";
    }

    cout << "\n===== SALARY INFORMATION =====\n";

    e.basicPay = getAmount("Basic Pay: Rs. ");
    e.medicalAllowance = getAmount("Medical Allowance: Rs. ");
    e.travelAllowance = getAmount("Travel Allowance: Rs. ");
    e.otherAllowance = getAmount("Other Allowance: Rs. ");

    cout << "\n===== LOAN INFORMATION =====\n";

    e.loanType = getText(
        "Loan Type (Bike/Car/Personal/None): ");

    e.loanAmount = getAmount("Total Loan Amount: Rs. ");

    while (true) {
        e.loanInstallment = getAmount(
            "Monthly Loan Installment: Rs. ");

        if (e.loanInstallment <= e.loanAmount)
            break;

        cout << "Installment cannot exceed the total loan amount!\n";
    }

    cout << "\n===== INSURANCE INFORMATION =====\n";

    e.insurance = getText(
        "Insurance Type/Provider (None if no insurance): ");

    e.insurancePremium = getAmount(
        "Monthly Insurance Premium: Rs. ");

    if (e.loanInstallment + e.insurancePremium >
        e.grossSalary()) {
        cout << "Error: Deductions cannot exceed gross salary.\n";
        cout << "Employee registration cancelled.\n";
        return;
    }

    employees.push_back(e);
    saveData();

    cout << "\nEmployee registered successfully!\n";

    displayEmployee(e);
}

// Display all employees
void displayAll() {
    if (employees.empty()) {
        cout << "\nNo employee records found!\n";
        return;
    }

    cout << "\n========== ALL EMPLOYEES ==========\n";

    for (const auto& e : employees) {
        displayEmployee(e);
    }
}

// Search employee
void searchEmployee() {
    string id = getText("\nEnter Employee ID to search: ");

    transform(id.begin(), id.end(), id.begin(),
              [](unsigned char c) {
                  return toupper(c);
              });

    for (const auto& e : employees) {
        if (e.id == id) {
            cout << "\nEmployee found successfully!\n";
            displayEmployee(e);
            return;
        }
    }

    cout << "\nEmployee not found!\n";
}

// Delete employee
void deleteEmployee() {
    string id = getText("\nEnter Employee ID to delete: ");

    transform(id.begin(), id.end(), id.begin(),
              [](unsigned char c) {
                  return toupper(c);
              });

    auto it = find_if(employees.begin(), employees.end(),
        [&](const Employee& e) {
            return e.id == id;
        });

    if (it != employees.end()) {
        employees.erase(it);
        saveData();

        cout << "\nEmployee record deleted successfully!\n";
    }
    else {
        cout << "\nEmployee ID not found!\n";
    }
}

// Main menu
int main() {

    loadData();

    int choice;

    cout << "========================================\n";
    cout << "       WELCOME TO DIGICODE BYTE BANK\n";
    cout << "       EMPLOYEE MANAGEMENT SYSTEM\n";
    cout << "========================================\n";

    do {
        cout << "\n=============== MAIN MENU ===============\n";
        cout << "1. Add New Employee\n";
        cout << "2. Display All Employees\n";
        cout << "3. Search Employee\n";
        cout << "4. Delete Employee\n";
        cout << "5. Exit\n";
        cout << "==========================================\n";

        cout << "Enter your choice: ";

        string input;
        getline(cin, input);
        stringstream ss(input);

        if (!(ss >> choice) || (ss >> ws && !ss.eof())) {
            choice = 0;
        }

        switch (choice) {

            case 1:
                addEmployee();
                break;

            case 2:
                displayAll();
                break;

            case 3:
                searchEmployee();
                break;

            case 4:
                deleteEmployee();
                break;

            case 5:
                saveData();
                cout << "\nThank you for using "
                        "DigiCode Byte Bank!\n";
                break;

            default:
                cout << "\nInvalid choice! Try again.\n";
        }

    } while (choice != 5);

    return 0;
}
```
