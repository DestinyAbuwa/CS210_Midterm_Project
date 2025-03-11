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

  School();
  School(string n, string a, string c, string s, string co) : name (n), address(a), city(c), state(s), county(co){}
};

struct TreeNode
{
  School school;
  TreeNode* left;
  TreeNode* right;
  TreeNode(School s) : school(s), left(nullptr), right(nullptr) {}
};


