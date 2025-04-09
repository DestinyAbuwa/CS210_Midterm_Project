# CS210_Midterm_Project
# Destiny Abuwa
```
#include <iostream>
#include <fstream>
#include <sstream>
#include <string>
#include <vector>
#include <list>
#include "timer.h"

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
            // Node found, handle deletion cases

            // Case 1: No child (Leaf node)
            if (!node->left && !node->right)
            {
                delete node;
                return nullptr;
            }
            // Case 2: One child
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
            // Case 3: Two children
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
            return findNode(node->left, name);
        else
            return findNode(node->right, name);
    }

    // Helper function for in-order traversal
    void InOrder(TreeNode* node)
    {
        if (!node) return;
        InOrder(node->left);
        printSchool(node->school);
        InOrder(node->right);
    }

    // Helper function for pre-order traversal
    void PreOrder(TreeNode* node)
    {
        if (!node) return;
        printSchool(node->school);
        PreOrder(node->left);
        PreOrder(node->right);
    }

    // Helper function for post-order traversal
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
        TreeNode* result = findNode(root, name);
        root = deleteNode(root, name);
        if (!root)
        {
            cout << "School not found." << endl;
        }
        else if (result)
        {
            cout << "Deleted school: " << name << endl;
        }
        else
        {
            cout << "School not found." << endl;
        }

    }

    void findByName(string name)
    {
        TreeNode* result = findNode(root, name);
        if (result)
            printSchool(result->school);
        else
            cout << "School not found.\n";
    }

    void printSchool(School school)
    {
        cout << "Name: " << school.name << ", Address: " << school.address
             << ", City: " << school.city << ", State: " << school.state
             << ", County: " << school.county << endl;
    }

    void displayInOrder() { InOrder(root); }
    void displayPreOrder() { PreOrder(root); }
    void displayPostOrder() { PostOrder(root); }

};

void CSVReaderBST(SchoolBST& bst, const string& filename)
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

void CSVReaderList(SchoolList& list, const string& filename)
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

void CSVReaderHash(SchoolHashTable &hashTable, const string &filename)
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

void testDeletionList(SchoolList& list)
{
    list.deleteByName("GLEN OAK COMM LEARNING CNTR");
    list.deleteByName("ROBERT A JAMIESON SCHOOL");
    list.deleteByName("HARRISON COMM LEARNING CNTR");
    list.deleteByName("ELMWOOD ELEM SCHOOL");
    list.deleteByName("PLEASANT HILL ELEM SCHOOL");
    list.deleteByName("PEORIA HEIGHTS HIGH SCHOOL");
    list.deleteByName("DUNLAP VALLEY MIDDLE SCHOOL");
    list.deleteByName("BRIMFIELD GRADE SCHOOL");
    list.deleteByName("PEORIA REGIONAL HIGH SCHOOL");
    list.deleteByName("PEORIA HEIGHTS GRADE SCHOOL");
    
}
void testDeletionBST(SchoolBST& bst)
{
    bst.deleteByName("GLEN OAK COMM LEARNING CNTR");
    bst.deleteByName("ROBERT A JAMIESON SCHOOL");
    bst.deleteByName("HARRISON COMM LEARNING CNTR");
    bst.deleteByName("ELMWOOD ELEM SCHOOL");
    bst.deleteByName("PLEASANT HILL ELEM SCHOOL");
    bst.deleteByName("PEORIA HEIGHTS HIGH SCHOOL");
    bst.deleteByName("DUNLAP VALLEY MIDDLE SCHOOL");
    bst.deleteByName("BRIMFIELD GRADE SCHOOL");
    bst.deleteByName("PEORIA REGIONAL HIGH SCHOOL");
    bst.deleteByName("PEORIA HEIGHTS GRADE SCHOOL");
}
void testDeletionHash(SchoolHashTable& hash)
{
    hash.deleteByName("GLEN OAK COMM LEARNING CNTR");
    hash.deleteByName("ROBERT A JAMIESON SCHOOL");
    hash.deleteByName("HARRISON COMM LEARNING CNTR");
    hash.deleteByName("ELMWOOD ELEM SCHOOL");
    hash.deleteByName("PLEASANT HILL ELEM SCHOOL");
    hash.deleteByName("PEORIA HEIGHTS HIGH SCHOOL");
    hash.deleteByName("DUNLAP VALLEY MIDDLE SCHOOL");
    hash.deleteByName("BRIMFIELD GRADE SCHOOL");
    hash.deleteByName("PEORIA REGIONAL HIGH SCHOOL");
    hash.deleteByName("PEORIA HEIGHTS GRADE SCHOOL");
}

void testSearchList(SchoolList& list)
{
    list.findByName("GLEN OAK COMM LEARNING CNTR");
    list.findByName("ROBERT A JAMIESON SCHOOL");
    list.findByName("HARRISON COMM LEARNING CNTR");
    list.findByName("ELMWOOD ELEM SCHOOL");
    list.findByName("PLEASANT HILL ELEM SCHOOL");
    list.findByName("PEORIA HEIGHTS HIGH SCHOOL");
    list.findByName("DUNLAP VALLEY MIDDLE SCHOOL");
    list.findByName("BRIMFIELD GRADE SCHOOL");
    list.findByName("PEORIA REGIONAL HIGH SCHOOL");
    list.findByName("PEORIA HEIGHTS GRADE SCHOOL");
}
void testSearchBST(SchoolBST& bst)
{
    bst.findByName("GLEN OAK COMM LEARNING CNTR");
    bst.findByName("ROBERT A JAMIESON SCHOOL");
    bst.findByName("HARRISON COMM LEARNING CNTR");
    bst.findByName("ELMWOOD ELEM SCHOOL");
    bst.findByName("PLEASANT HILL ELEM SCHOOL");
    bst.findByName("PEORIA HEIGHTS HIGH SCHOOL");
    bst.findByName("DUNLAP VALLEY MIDDLE SCHOOL");
    bst.findByName("BRIMFIELD GRADE SCHOOL");
    bst.findByName("PEORIA REGIONAL HIGH SCHOOL");
    bst.findByName("PEORIA HEIGHTS GRADE SCHOOL");
}
void testSearchHash(SchoolHashTable& hash)
{
    hash.findByName("GLEN OAK COMM LEARNING CNTR");
    hash.findByName("ROBERT A JAMIESON SCHOOL");
    hash.findByName("HARRISON COMM LEARNING CNTR");
    hash.findByName("ELMWOOD ELEM SCHOOL");
    hash.findByName("PLEASANT HILL ELEM SCHOOL");
    hash.findByName("PEORIA HEIGHTS HIGH SCHOOL");
    hash.findByName("DUNLAP VALLEY MIDDLE SCHOOL");
    hash.findByName("BRIMFIELD GRADE SCHOOL");
    hash.findByName("PEORIA REGIONAL HIGH SCHOOL");
    hash.findByName("PEORIA HEIGHTS GRADE SCHOOL");
}


int main()
{
    Timer t;
    string s = "runtime_analysis.csv";
    ofstream outfile(s);
    if (!outfile.is_open())
    {
        cerr << "Failed to open runtime_analysis.csv for writing.\n";
        return 1;
    }
    outfile << "List/BST/Hash, Operation, Dataset, Time taken (microseconds)\n";

    SchoolList list1;
    SchoolList list2;
    SchoolList list3;

    double timeElapsed_insert_list1 = t.time_function(CSVReaderList, list1, "Illinois_Peoria_Schools.csv");
    outfile << "List, Insertion, Illinois_Peoria_Schools.csv, " << timeElapsed_insert_list1 << endl;
    double timeElapsed_insert_list2 = t.time_function(CSVReaderList, list2, "Illinois_Schools.csv");
    outfile << "List, Insertion, Illinois_Schools.csv, " << timeElapsed_insert_list2 << endl;
    double timeElapsed_insert_list3 = t.time_function(CSVReaderList, list3, "USA_Schools.csv");
    outfile << "List, Insertion, USA_Schools.csv, " << timeElapsed_insert_list3 << endl;

    double timeElapsed_search_list1 = t.time_function(testSearchList, list1);
    outfile << "List, Searching, Illinois_Peoria_Schools.csv, " << timeElapsed_search_list1 << endl;
    double timeElapsed_search_list2 = t.time_function(testSearchList, list2);
    outfile << "List, Searching, Illinois_Schools.csv, " << timeElapsed_search_list2 << endl;
    double timeElapsed_search_list3 = t.time_function(testSearchList, list3);
    outfile << "List, Searching, USA_Schools.csv, " << timeElapsed_search_list3 << endl;

    double timeElapsed_delete_list1 = t.time_function(testDeletionList, list1);
    outfile << "List, Deletion, Illinois_Peoria_Schools.csv, " << timeElapsed_delete_list1 << endl;
    double timeElapsed_delete_list2 = t.time_function(testDeletionList, list2);
    outfile << "List, Deletion, Illinois_Schools.csv, " << timeElapsed_delete_list2 << endl;
    double timeElapsed_delete_list3 = t.time_function(testDeletionList, list3);
    outfile << "List, Deletion, USA_Schools.csv, " << timeElapsed_delete_list3 << endl;



    SchoolBST bst1;
    SchoolBST bst2;
    SchoolBST bst3;

    double timeElapsed_insert_bst1 = t.time_function(CSVReaderBST, bst1, "Illinois_Peoria_Schools.csv");
    outfile << "BST, Insertion, Illinois_Peoria_Schools.csv, " << timeElapsed_insert_bst1 << endl;
    double timeElapsed_insert_bst2 = t.time_function(CSVReaderBST, bst2, "Illinois_Schools.csv");
    outfile << "BST, Insertion, IIllinois_Schools.csv, " << timeElapsed_insert_bst2 << endl;
    double timeElapsed_insert_bst3 = t.time_function(CSVReaderBST, bst3, "USA_Schools.csv");
    outfile << "BST, Insertion, USA_Schools.csv, " << timeElapsed_insert_bst3 << endl;

    double timeElapsed_search_bst1 = t.time_function(testSearchBST, bst1);
    outfile << "BST, Searching, Illinois_Peoria_Schools.csv, " << timeElapsed_search_bst1 << endl;
    double timeElapsed_search_bst2 = t.time_function(testSearchBST, bst2);
    outfile << "BST, Searching, IIllinois_Schools.csv, " << timeElapsed_search_bst2 << endl;
    double timeElapsed_search_bst3 = t.time_function(testSearchBST, bst3);
    outfile << "BST, Searching, USA_Schools.csv, " << timeElapsed_search_bst3 << endl;

    double timeElapsed_delete_bst1 = t.time_function(testDeletionBST, bst1);
    outfile << "BST, Deletion, Illinois_Peoria_Schools.csv, " << timeElapsed_delete_bst1 << endl;
    double timeElapsed_delete_bst2 = t.time_function(testDeletionBST, bst2);
    outfile << "BST, Deletion, IIllinois_Schools.csv, " << timeElapsed_delete_bst2 << endl;
    double timeElapsed_delete_bst3 = t.time_function(testDeletionBST, bst3);
    outfile << "BST, Deletion, USA_Schools.csv, " << timeElapsed_delete_bst3 << endl;



    SchoolHashTable hash1;
    SchoolHashTable hash2;
    SchoolHashTable hash3;

    double timeElapsed_insert_hash1 = t.time_function(CSVReaderHash, hash1, "Illinois_Peoria_Schools.csv");
    outfile << "Hash, Insertion, Illinois_Peoria_Schools.csv, " << timeElapsed_insert_hash1 << endl;
    double timeElapsed_insert_hash2 = t.time_function(CSVReaderHash, hash2, "Illinois_Schools.csv");
    outfile << "Hash, Insertion, IIllinois_Schools.csv, " << timeElapsed_insert_hash2 << endl;
    double timeElapsed_insert_hash3 = t.time_function(CSVReaderHash, hash3, "USA_Schools.csv");
    outfile << "Hash, Insertion, USA_Schools.csv, " << timeElapsed_insert_hash3 << endl;

    double timeElapsed_search_hash1 = t.time_function(testSearchHash, hash1);
    outfile << "Hash, Searching, Illinois_Peoria_Schools.csv, " << timeElapsed_search_hash1 << endl;
    double timeElapsed_search_hash2 = t.time_function(testSearchHash, hash2);
    outfile << "Hash, Searching, IIllinois_Schools.csv, " << timeElapsed_search_hash2 << endl;
    double timeElapsed_search_hash3 = t.time_function(testSearchHash, hash3);
    outfile << "Hash, Searching, USA_Schools.csv, " << timeElapsed_search_hash3 << endl;

    double timeElapsed_delete_hash1 = t.time_function(testDeletionHash, hash1);
    outfile << "Hash, Deletion, Illinois_Peoria_Schools.csv, " << timeElapsed_delete_hash1 << endl;
    double timeElapsed_delete_hash2 = t.time_function(testDeletionHash, hash2);
    outfile << "Hash, Deletion, IIllinois_Schools.csv, " << timeElapsed_delete_hash2 << endl;
    double timeElapsed_delete_hash3 = t.time_function(testDeletionHash, hash3);
    outfile << "Hash, Deletion, USA_Schools.csv, " << timeElapsed_delete_hash3 << endl;



    return 0;
}

