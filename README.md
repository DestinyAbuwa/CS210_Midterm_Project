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
      }
};

void CSVReader()
{
}

int main()
{
}

