#include <iostream>
#include <string>
#include <vector>
#include <fstream>

using namespace std;

// Address Class "Composition"
class Address {
    string street, city, state;

public:
    void getAddress() {
        cout << "Enter Street: ";
        cin.ignore(); // To handle newline
        getline(cin, street);
        cout << "Enter City: ";
        getline(cin, city);
        cout << "Enter State: ";
        getline(cin, state);
    }
    void displayAddress() const {
        cout << "Address: " << street << ", " << city << ", " << state << endl;
    }
};

// Main Student Class
class Student {
protected: // Protected to allow access in derived classes
    int roll;
    string name;
    Address address;

public:
    void getStudentData() {
        cout << "Enter Roll Number: ";
        cin >> roll;
        cin.ignore(); // Consume newline
        cout << "Enter Name: ";
        getline(cin, name);
        cout << "Enter Address Information:\n";
        address.getAddress();
    }

    void displayStudentData() const {
        cout << "Roll Number: " << roll << "\nName: " << name << endl;
        address.displayAddress();
    }

    friend class Marks; // Marks class declared as a friend
};

// Marks Class
class Marks {
protected:
    int english, maths, science;

public:
    Marks() : english(0), maths(0), science(0) {}

    void getMarks() {
        cout << "Enter English Marks: ";
        cin >> english;
        cout << "Enter Maths Marks: ";
        cin >> maths;
        cout << "Enter Science Marks: ";
        cin >> science;
    }

    void displayMarks() const {
        cout << "Marks - English: " << english << ", Maths: " << maths << ", Science: " << science << endl;
    }

    int calculateTotal() const {
        return english + maths + science;
    }

    float calculatePercentage() const {
        return calculateTotal() / 3.0f;
    }
};

// Derived Class for Scoring
class Score : public Student, public Marks {
    float percentage;

public:
    void input() {
        getStudentData();
        getMarks();
        percentage = calculatePercentage();
    }

    void display() const {
        displayStudentData();
        displayMarks();
        cout << "Total Percentage: " << percentage << "%" << endl;
        displayGrade();
    }

    void displayGrade() const {
        if (percentage >= 85) {
            cout << "Grade: A+" << endl;
        } else if (percentage >= 60) {
            cout << "Grade: A" << endl;
        } else if (percentage >= 50) {
            cout << "Grade: B" << endl;
        } else {
            cout << "Grade: C" << endl;
        }
    }

    void saveToFile() const {
        ofstream file("result.txt", ios::app);
        if (file.is_open()) {
            file << "Roll Number: " << roll << "\nName: " << name << "\n";
            file << "Address: ";
            file << "Marks - English(" << english << "), Maths(" << maths << "), Science(" << science << ")\n";
            file << "Total Percentage: " << percentage << "%\n";
            file << "==============================\n";
            file.close();
            cout << "Data saved to result.txt\n";
        } else {
            cout << "Error saving to file.\n";
        }
    }
};

// Menu-Driven Program
void menu() {
    vector<Score> students;
    int choice;

    do {
        cout << "\nStudent Information System\n";
        cout << "1. Add Student\n";
        cout << "2. View All Students\n";
        cout << "3. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1: {
                Score student;
                student.input();
                students.push_back(student);
                student.saveToFile();
                break;
            }
            case 2: {
                if (students.empty()) {
                    cout << "No students available.\n";
                } else {
                    for (size_t i = 0; i < students.size(); ++i) {
                        cout << "\nStudent " << i + 1 << ":\n";
                        students[i].display();
                    }
                }
                break;
            }
            case 3:
                cout << "Exiting program.\n";
                break;
            default:
                cout << "Invalid choice. Try again.\n";
        }
    } while (choice != 3);
}

// Main Function
int main() {
    menu();
    return 0;
}

