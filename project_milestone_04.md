# CS210_Midterm_Project
# Destiny Abuwa
```

#include <iostream>
#include <vector>
#include <list>
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

  School(string n, string a, string c, string s, string co) : name (n), address(a), city(c), state(s), county(co) {}
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
    void display()
    {
      for (int i = 0; i < TABLE_SIZE; i++)
      {
        if (!table[i].empty())
        {
          cout << "Index " << i << ": ";
          for (const auto &school : table[i])
          {
            cout << "(" << school.name << ", " << school.address << ", " << school.city << ", " << school.state << ", " << school.county << ") ";
          }
          cout << endl;
        }
      }
    }
};

// Function to load data from CSV file for hashtable
void loadFromCSV(SchoolHashTable &hashTable, const string &filename)
{
  ifstream file(filename);
  if (!file)
  {
    cerr << "Error opening file!" << endl;
    return;
  }

  string line;
  while (getline(file, line))
  {
    stringstream ss(line);
    string name, address, city, state, county;
    getline(ss, name, ',');
    getline(ss, address, ',');
    getline(ss, city, ',');
    getline(ss, state, ',');
    getline(ss, county, ',');
    hashTable.insert(School(name, address, city, state, county));
  }
  file.close();
}

int main()
{
  SchoolHashTable hashTable;
  loadFromCSV(hashTable, "Illinois_Peoria_Schools.csv");

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
        hashTable.display();
      break;

      case 'b':
        cout << "Enter school name: ";
        getline(cin, name);
        if (School* school = hashTable.findByName(name))
        {
          cout << "Found: " << school->name << ", " << school->address << ", " << school->city << ", " << school->state << ", " << school->county << "\n";
        }
        else
        {
          cout << "School not found!\n";
        }
      break;

      case 'c':
        cout << "Enter school name to delete: ";
        getline(cin, name);
        hashTable.deleteByName(name);
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
