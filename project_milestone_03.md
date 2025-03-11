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

class SchoolBST
{
  private:
  TreeNode* root;

  TreeNode* insertNode(TreeNode* node, School school)
  {
    if (node == nullptr)
    {
      return new TreeNode(school);
    }
    if (school.name < node->school.name)
    {
      node->left = insertNode(node->left, school);
    }
    else
    {
      node->right = insertNode(node->right, school);
    }
    return node;
  }

  void insert(School school)
  {
    root = insertNode(root, school);
  }




};

