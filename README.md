# DSA_Project
C++ Library Management System A console-based app using a Binary Search Tree (BST) for fast inventory management.  ✨ Features: Admin login, add/delete books, efficient ID searching, issue/return tracking, and sorted display.  Built with standard C++ OOP principles. A perfect project for learning data structures!
Username: admin Password: pass123

#include <iostream>
#include <string>

using namespace std;

// ================= BOOK CLASS =================
class Book {
public:
    int id;
    string title;
    string author;
    string genre;
    bool isAvailable;

    Book() {}

    Book(int id, string title, string author, string genre, bool available = true) {
        this->id = id;
        this->title = title;
        this->author = author;
        this->genre = genre;
        this->isAvailable = available;
    }

    void display() const {
        cout << "ID: " << id
            << " | Title: " << title
            << " | Author: " << author
            << " | Genre: " << genre
            << " | Available: " << (isAvailable ? "Yes" : "No")
            << endl;
    }
};

// ================= BST NODE =================
class BSTNode {
public:
    Book data;
    BSTNode* left;
    BSTNode* right;

    BSTNode(Book book) : data(book), left(nullptr), right(nullptr) {}
};

// ================= LIBRARY BST =================
class LibraryBST {
private:
    BSTNode* root;

    BSTNode* insert(BSTNode* node, Book book) {
        if (node == nullptr)
            return new BSTNode(book);

        if (book.id < node->data.id)
            node->left = insert(node->left, book);
        else if (book.id > node->data.id)
            node->right = insert(node->right, book);

        return node;
    }

    Book* search(BSTNode* node, int id) {
        if (node == nullptr)
            return nullptr;

        if (node->data.id == id)
            return &node->data;

        if (id < node->data.id)
            return search(node->left, id);
        else
            return search(node->right, id);
    }

    void inorder(BSTNode* node) const {
        if (!node) return;
        inorder(node->left);
        node->data.display();
        inorder(node->right);
    }

    BSTNode* findMin(BSTNode* node) {
        while (node && node->left)
            node = node->left;
        return node;
    }

    BSTNode* deleteNode(BSTNode* node, int id) {
        if (!node) return nullptr;

        if (id < node->data.id)
            node->left = deleteNode(node->left, id);
        else if (id > node->data.id)
            node->right = deleteNode(node->right, id);
        else {
            if (!node->left && !node->right) {
                delete node;
                return nullptr;
            }
            else if (!node->left) {
                BSTNode* temp = node->right;
                delete node;
                return temp;
            }
            else if (!node->right) {
                BSTNode* temp = node->left;
                delete node;
                return temp;
            }
            BSTNode* temp = findMin(node->right);
            node->data = temp->data;
            node->right = deleteNode(node->right, temp->data.id);
        }
        return node;
    }

public:
    LibraryBST() : root(nullptr) {}

    void addBook(Book book) {
        root = insert(root, book);
    }

    Book* searchBook(int id) {
        return search(root, id);
    }

    void deleteBook(int id) {
        root = deleteNode(root, id);
    }

    void displayAll() {
        if (!root)
            cout << "Library is empty.\n";
        else
            inorder(root);
    }
};

// ================= MAIN =================
int main() {
    string username, password;
    bool loginSuccess = false;

    system("color B");

    cout << "===== SECURITY CHECK =====\n";

    while (!loginSuccess) {
        cout << "Username: ";
        cin >> username;
        cout << "Password: ";
        cin >> password;

        if (username == "admin" && password == "pass123") {
            cout << "\nAccess Granted. Welcome, Admin!\n";
            loginSuccess = true;
        }
        else {
            cout << "Invalid credentials. Try again.\n";
        }
    }

    LibraryBST library;
    int choice;

    do {
        system("cls");

        cout << "\n===== Library Management System =====\n";
        cout << "1. Add Book\n";
        cout << "2. Display All Books\n";
        cout << "3. Search Book by ID\n";
        cout << "4. Issue / Return Book\n";
        cout << "5. Delete Book\n";
        cout << "0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        // ADD BOOK
        if (choice == 1) {
            int id;
            string title, author, genre;

            cout << "Enter Book ID: ";
            cin >> id;
            cin.ignore();

            if (library.searchBook(id)) {
                cout << "Error: Book ID already exists.\n";
            }
            else {
                cout << "Enter Title: ";
                getline(cin, title);
                cout << "Enter Author: ";
                getline(cin, author);
                cout << "Enter Genre: ";
                getline(cin, genre);

                library.addBook(Book(id, title, author, genre));
                cout << "Book added successfully!\n";
            }
            system("pause");
        }
        // DISPLAY ALL
        else if (choice == 2) {
            library.displayAll();
            cout << "\nPress 1 to return to Menu, or 0 to Exit: ";
            int sub; cin >> sub;
            if (sub == 0) return 0;
        }

        // SEARCH BOOK
        else if (choice == 3) {
            int id;
            cout << "Enter Book ID: ";
            cin >> id;

            Book* book = library.searchBook(id);
            if (book) book->display();
            else cout << "Book not found!\n";

            cout << "\nPress 1 to return to Menu, or 0 to Exit: ";
            int sub; cin >> sub;
            if (sub == 0) return 0;
        }

        // ISSUE / RETURN
        else if (choice == 4) {
            int id;
            cout << "Enter Book ID: ";
            cin >> id;

            Book* book = library.searchBook(id);
            if (book) {
                int action;
                cout << "1. Issue Book\n2. Return Book\nEnter choice: ";
                cin >> action;

                if (action == 1 && book->isAvailable) {
                    book->isAvailable = false;
                    cout << "Book issued successfully!\n";
                }
                else if (action == 2 && !book->isAvailable) {
                    book->isAvailable = true;
                    cout << "Book returned successfully!\n";
                }
                else {
                    cout << "Invalid operation.\n";
                }
            }
            else {
                cout << "Book not found!\n";
            }

            cout << "\nPress 1 to return to Menu, or 0 to Exit: ";
            int sub; cin >> sub;
            if (sub == 0) return 0;
        }

        // DELETE BOOK
        else if (choice == 5) {
            int id;
            cout << "Enter Book ID to delete: ";
            cin >> id;

            if (library.searchBook(id)) {
                library.deleteBook(id);
                cout << "Book deleted successfully!\n";
            }
            else {
                cout << "Book not found!\n";
            }

            cout << "\nPress 1 to return to Menu, or 0 to Exit: ";
            int sub; cin >> sub;
            if (sub == 0) return 0;
        }

    } while (choice != 0);

    cout << "Exiting system. Goodbye!\n";
    return 0;
}
