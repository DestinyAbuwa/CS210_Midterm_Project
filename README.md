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

class SchoolHashTable
{
  private:
    static const int TABLE_SIZE = 100;
    vector<list<School>> table;

    // Hash function: Modulo Hashing
    int hashFunction(string key)
    {
      int hash = 0;
      for (char ch : key)
      {
        hash += ch;
      }
      return hash % TABLE_SIZE;
    }

public:
    SchoolHashTable()
    {
      table.resize(TABLE_SIZE);
    }

    // Insert a school into the hash table
    void insert(School school)
    {
      int index = hashFunction(school.name);
      table[index].push_back(school);
    }

    // Delete a school by name
    void deleteByName(string name)
    {
      int index = hashFunction(name);
      auto &chain = table[index];
      for (auto it = chain.begin(); it != chain.end(); ++it)
      {
        if (it->name == name)
        {
          chain.erase(it);
          cout << "Deleted school: " << name << endl;
          return;
        }
      }
      cout << "School not found!" << endl;
    }

    // Find a school by name
    School* findByName(string name)
    {
      int index = hashFunction(name);
      for (auto &school : table[index])
      {
        if (school.name == name)
        {
          return &school;
        }
      }
      return nullptr;
    }

    // Display all schools in the hash table
    void display() {
        
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

