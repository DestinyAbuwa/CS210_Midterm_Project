# CS210_Midterm_Project
# Destiny Abuwa
```
#include <iostream>
#include <fstream>
#include <sstream>
#include <string>

using namespace std;

struct School
{
  string name;
  string address;
  string city;
  string state;
  string county;
  School* next;

  School(string n, string a, string c, string s, string co) : name (n), address(a), city(c), state(s), county(co), next(nullptr) {}
};

class SchoolList
{
  private:
      School* head;

  public:
      SchoolList() : head(nullptr) {}

      void insertFirst(School* school)
      {
        school->next = head;
        head = school;
      }

      void insertLast(School* school)
      {
        if(!head)
        {
          head = school;
        }
        else
        {
          School* temp = head;
          while(temp->next)
          {
            temp = temp->next;
          }
          temp->next = school;
        }
      }

      bool deleteByName(string name)
      {
        School* toDelete = findByName(name);
        if (toDelete == nullptr)
        {
          return false;
        }
        else if (toDelete == head)
        {
          School* temp = head;
          head = head->next;
          delete temp;
          return true;
        }
        else
        {
          School* temp = head;
          while(temp->next != toDelete)
          {
            temp = temp->next;
          }
          if (temp->next)
          {
            temp->next = toDelete->next;
            delete toDelete;
            return true;
          }
          return false;
        }
      }

      School* findByName(string name)
      {
        School* temp = head;
        while(temp)
        {
          if (temp->name == name)
          {
            return temp;
          }
          temp = temp->next;
        }
        return nullptr;
      }

      void display()
      {
        School* temp = head;
        while(temp)
        {
          cout << temp->name << " - " << temp->address << ", " << temp->city << ", " << temp->state << " (" << temp->county << ")\n";
          temp = temp->next;
        }
      }
};

void CSVReader(SchoolList& list, const string& filename)
{
  ifstream file(filename);
  if (!file.is_open())
  {
    cerr << "Error: Could not open file " << endl;
    return;
  }
  string line;
  string name;
  string address;
  string city;
  string state;
  string county;

  while (getline(file, line))
  {
    stringstream ss(line);
    getline(ss, name, ',');
    getline(ss, address, ',');
    getline(ss, city, ',');
    getline(ss, state, ',');
    getline(ss, county, ',');
    list.insertLast(new School(name, address, city, state, county));
  }
  file.close();
}

int main()
{
  SchoolList list;
  CSVReader(list, "Illinois_Peoria_Schools.csv");

  char choice;
  string name;
  do
  {
    cout << "\nMenu:\na) Display Schools\nb) Search School\nc) Delete School\nd) Exit\nChoice: ";
    cin >> choice;
    cin.ignore();
    switch (choice)
    {
      case 'a':
          list.display();
      break;

      case 'b':
        cout << "Enter school name to search: ";
        getline(cin, name);
        if (School* school = list.findByName(name))
        {
          cout << "Found: " << school->name << " - " << school->address << endl;
        }
        else
        {
          cout << "School not found!\n";
        }
      break;

      case 'c':
        cout << "Enter school name to delete: ";
        getline(cin, name);
        if (list.deleteByName(name))
        {
          cout << "School deleted!\n";
        }
        else
        {
          cout << "School not found!\n";
        }
      break;

      case 'd':
        cout << "Exiting...\n";
      break;

      default:
        cout << "Invalid choice!\n";
    }
  }
  while (choice != 'd');

  return 0;
}

