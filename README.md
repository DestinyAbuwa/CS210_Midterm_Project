# CS210_Midterm_Project
# Destiny Abuwa

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
      }
      bool deleteByName(string name)
      {
      }
      School* findByName(string name)
      {
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

