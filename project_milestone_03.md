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

  TreeNode* findNode(TreeNode* node, string name)
  {
    if (!node || node->school.name == name) return node;

    if (name < node->school.name)
    {
      return findNode(node->left, name);
    }
    else
    {
      return findNode(node->right, name);
    }
  }
  
  void InOrder(TreeNode* node)
  {
    if (!node) return;
    InOrder(node->left);
    printSchool(node->school);
    InOrder(node->right);
  }

  void PreOrder(TreeNode* node)
  {
    if (!node) return;
    printSchool(node->school);
    PreOrder(node->left);
    PreOrder(node->right);
  }

  void PostOrder(TreeNode* node)
  {
    if (!node) return;
    PostOrder(node->left);
    PostOrder(node->right);
    printSchool(node->school);
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
    if (!root)
    {
      cout << "Node not found." << endl;
    }
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

  void displayInOrder()
  {
    InOrder(root);
  }
  void displayPreOrder()
  {
    PreOrder(root);
  }
  void displayPostOrder()
  {
   PostOrder(root);
  }

};

void CSVReader(SchoolBST& bst, const string& filename)
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

    School school(name, address, city, state, county);
    bst.insert(school);
  }
  file.close();
}

int main()
{
  SchoolBST bst;
  CSVReader(bst, "Illinois_Peoria_Schools.csv");

  char choice;
  string name;
  do
  {
    cout << "\nMenu:\na) Search School\nb) Delete School\nc) Display Schools by InOrder\nd) Display Schools by PreOrder\ne) Display Schools by PostOrder\nf) Exit\nChoice: ";
    cin >> choice;
    cin.ignore();
    switch (choice)
    {
      case 'a':
          cout << "Enter school name to search: ";
          getline(cin, name);
          bst.findByName(name);
      break;

      case 'b':
          cout << "Enter school name to delete: ";
          getline(cin, name);
          bst.deleteByName(name);
      break;

      case 'c':
          bst.displayInOrder();
      break;

      case 'd':
          bst.displayPreOrder();
      break;

      case 'e':
          bst.displayPostOrder();
      break;

      case 'f':
          cout << "Exiting...\n";
      break;

      default:
          cout << "Invalid choice!\n";
    }
  }
  while (choice != 'f');

  return 0;
}


