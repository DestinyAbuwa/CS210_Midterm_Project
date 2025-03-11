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

  TreeNode* findMin(TreeNode* node)
  {
    while (node->left)
    {
      node = node->left;
    }
    return node;
  }

  TreeNode* deleteNode(TreeNode* node, string name)
  {
    if (!node) return nullptr;

    if (name < node->school.name)
    {
      node->left = deleteNode(node->left, name);
    }
    else if (name > node->school.name)
    {
      node->right = deleteNode(node->right, name);
    }
    else
    {
      if (!node->left && !node->right)
      {
        delete node;
        return nullptr;
      }

      if (!node->left)
      {
        TreeNode* temp = node->right;
        delete node;
        return temp;
      }
      else if (!node->right)
      {
        TreeNode* temp = node->left;
        delete node;
        return temp;
      }

      TreeNode* temp = findMin(node->right);
      node->school = temp->school;
      node->right = deleteNode(node->right, temp->school.name);
    }
    return node;
  }


  public:
  SchoolBST() : root(nullptr){};

  void insert(School school)
  {
    root = insertNode(root, school);
  }

  void deleteByName(string name)
  {
    root = deleteNode(root, name);
  }  

  void findByName(string name)
  {
    TreeNode* result = findNode(root, name);
    if (result)
    {
      printSchool(result->school);
    }
    else
    {
      cout << "School not found.\n";
    }
  }

  void printSchool(School school)
  {
    cout << "Name: " << school.name << ", Address: " << school.address
    << ", City: " << school.city << ", State: " << school.state
    << ", County: " << school.county << endl;
  }

};

