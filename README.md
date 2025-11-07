1.
Searching and Sorting -- 

#include <iostream>
#include <cstring>
#include <cstdlib>
using namespace std;

struct Student {
    int id;
    char name[50];
    float cgpa;
};

void addStudent(Student*& students, int& count) {
    students = (Student*) realloc(students, (count + 1) * sizeof(Student));
    if (students == nullptr) {
        cout << "Memory allocation failed!\n";
        exit(1);
    }
    cout << "\nEnter details for student " << count + 1 << ":\n";
    cout << "ID: ";
    cin >> students[count].id;
    cin.ignore();
    cout << "Name: ";
    cin.getline(students[count].name, 50);
    cout << "CGPA: ";
    cin >> students[count].cgpa;
    count++;
    cout << "Student added successfully!\n";
}

void searchByID(Student* students, int count, int id) {
    bool found = false;
    for (int i = 0; i < count; i++) {
        if (students[i].id == id) {
            cout << "\nRecord Found:\n";
            cout << "ID: " << students[i].id << "\nName: " << students[i].name << "\nCGPA: " << students[i].cgpa << "\n";
            found = true;
            break;
        }
    }
    if (!found)
        cout << "\nNo record found with ID " << id << endl;
}

void sortByName(Student* students, int count) {
    for (int i = 0; i < count - 1; i++) {
        for (int j = 0; j < count - i - 1; j++) {
            if (strcmp(students[j].name, students[j + 1].name) > 0) {
                swap(students[j], students[j + 1]);
            }
        }
    }
    cout << "\nStudents sorted by Name (Alphabetically).\n";
}

void sortByCGPA(Student* students, int count, bool ascending = true) {
    for (int i = 0; i < count - 1; i++) {
        for (int j = 0; j < count - i - 1; j++) {
            if ((ascending && students[j].cgpa > students[j + 1].cgpa) ||
                (!ascending && students[j].cgpa < students[j + 1].cgpa)) {
                swap(students[j], students[j + 1]);
            }
        }
    }
    cout << "\nStudents sorted by CGPA (" << (ascending ? "Ascending" : "Descending") << ").\n";
}

void displayAll(Student* students, int count) {
    if (count == 0) {
        cout << "\nNo records to display.\n";
        return;
    }
    cout << "\n----- Student Records -----\n";
    for (int i = 0; i < count; i++) {
        cout << "ID: " << students[i].id
             << "\nName: " << students[i].name
             << "\nCGPA: " << students[i].cgpa << "\n-----------------\n";
    }
}

int main() {
    Student* students = nullptr;
    int count = 0;
    int choice;

    do {
        cout << "\n===== STUDENT DATABASE MENU =====\n";
        cout << "1. Add Student\n";
        cout << "2. Display All Students\n";
        cout << "3. Search by ID\n";
        cout << "4. Sort by Name (Alphabetical)\n";
        cout << "5. Sort by CGPA (Ascending)\n";
        cout << "6. Sort by CGPA (Descending)\n";
        cout << "0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
        case 1:
            addStudent(students, count);
            break;
        case 2:
            displayAll(students, count);
            break;
        case 3: {
            int id;
            cout << "Enter ID to search: ";
            cin >> id;
            searchByID(students, count, id);
            break;
        }
        case 4:
            sortByName(students, count);
            break;
        case 5:
            sortByCGPA(students, count, true);
            break;
        case 6:
            sortByCGPA(students, count, false);
            break;
        case 0:
            cout << "Exiting program...\n";
            break;
        default:
            cout << "Invalid choice! Try again.\n";
        }

    } while (choice != 0);

    free(students);
    return 0;
}

OR
AI search Algorithm –

#include <iostream>
#include <queue>
#include <vector>
#include <utility>
#include <unistd.h>
using namespace std;

struct Cell {
    int x, y;
};

int dx[] = {-1, 1, 0, 0};
int dy[] = {0, 0, -1, 1};

void printMaze(vector<vector<char>> &maze) {
    for (auto &row : maze) {
        for (auto &cell : row) {
            cout << cell << ' ';
        }
        cout << endl;
    }
    cout << endl;
}

bool bfs(vector<vector<char>> &maze, Cell start, Cell goal) {
    int n = maze.size();
    int m = maze[0].size();
    vector<vector<bool>> visited(n, vector<bool>(m, false));
    vector<vector<Cell>> parent(n, vector<Cell>(m, {-1, -1}));
    queue<Cell> q;
    q.push(start);
    visited[start.x][start.y] = true;
    bool found = false;

    while (!q.empty()) {
        Cell cur = q.front();
        q.pop();
        if (maze[cur.x][cur.y] == '0')
            maze[cur.x][cur.y] = '.';
        system("clear");
        printMaze(maze);
        usleep(100000);

        if (cur.x == goal.x && cur.y == goal.y) {
            found = true;
            break;
        }

        for (int i = 0; i < 4; i++) {
            int nx = cur.x + dx[i];
            int ny = cur.y + dy[i];
            if (nx >= 0 && ny >= 0 && nx < n && ny < m && !visited[nx][ny] && maze[nx][ny] != '1') {
                visited[nx][ny] = true;
                parent[nx][ny] = cur;
                q.push({nx, ny});
            }
        }
    }

    if (!found) {
        cout << "No path found!\n";
        return false;
    }

    Cell cur = goal;
    while (!(cur.x == start.x && cur.y == start.y)) {
        if (maze[cur.x][cur.y] != 'G')
            maze[cur.x][cur.y] = '*';
        cur = parent[cur.x][cur.y];
    }

    cout << "\nOptimal Path Found:\n";
    printMaze(maze);
    return true;
}

void updateMaze(vector<vector<char>> &maze) {
    int x, y;
    cout << "Enter wall coordinates to toggle (x y): ";
    cin >> x >> y;
    if (x >= 0 && y >= 0 && x < maze.size() && y < maze[0].size()) {
        maze[x][y] = (maze[x][y] == '1') ? '0' : '1';
    }
}

int main() {
    int n, m;
    cout << "Enter maze size (rows cols): ";
    cin >> n >> m;

    vector<vector<char>> maze(n, vector<char>(m));
    cout << "Enter maze layout:\n";
    cout << "Use 0 for open space, 1 for wall, S for start, G for goal.\n";
    Cell start, goal;

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> maze[i][j];
            if (maze[i][j] == 'S') start = {i, j};
            if (maze[i][j] == 'G') goal = {i, j};
        }
    }

    cout << "\nInitial Maze:\n";
    printMaze(maze);
    bfs(maze, start, goal);

    char choice;
    cout << "\nDo you want to modify the maze dynamically and rerun BFS? (y/n): ";
    cin >> choice;

    while (choice == 'y' || choice == 'Y') {
        updateMaze(maze);
        system("clear");
        printMaze(maze);
        bfs(maze, start, goal);
        cout << "\nModify again? (y/n): ";
        cin >> choice;
    }

    cout << "\nExiting Maze Navigator.\n";
    return 0;
}

Heap Sort – 

#include <iostream>
using namespace std;

void heapify(int arr[], int n, int i) {
    int largest = i;
    int left = 2 * i + 1;
    int right = 2 * i + 2;

    if (left < n && arr[left] > arr[largest])
        largest = left;

    if (right < n && arr[right] > arr[largest])
        largest = right;

    if (largest != i) {
        swap(arr[i], arr[largest]);
        heapify(arr, n, largest);
    }
}

void heapSort(int arr[], int n) {
    for (int i = n / 2 - 1; i >= 0; i--)
        heapify(arr, n, i);

    cout << "\nMax-Heap Built: ";
    for (int i = 0; i < n; i++)
        cout << arr[i] << " ";
    cout << endl;

    for (int i = n - 1; i > 0; i--) {
        swap(arr[0], arr[i]);
        heapify(arr, i, 0);
    }
}

void display(int arr[], int n) {
    for (int i = 0; i < n; i++)
        cout << arr[i] << " ";
    cout << endl;
}

int main() {
    int n;
    cout << "Enter number of elements: ";
    cin >> n;

    int* arr = new int[n];

    cout << "Enter " << n << " integers:\n";
    for (int i = 0; i < n; i++)
        cin >> arr[i];

    cout << "\nOriginal Array: ";
    display(arr, n);

    heapSort(arr, n);

    cout << "\nSorted Array (Ascending Order): ";
    display(arr, n);

    delete[] arr;
    return 0;
}




3.
Implement stack as an abstract data type using singly linked list and use this ADT in cpp for conversion of infix expression to postfix, 

#include <iostream>
#include <cctype>
#include <cstring>
using namespace std;

struct Node {
    char data;
    Node* next;
};

class Stack {
private:
    Node* top;

public:
    Stack() { top = nullptr; }

    void push(char value) {
        Node* newNode = new Node;
        newNode->data = value;
        newNode->next = top;
        top = newNode;
    }

    char pop() {
        if (isEmpty()) {
            cout << "Stack Underflow!\n";
            return '\0';
        }
        Node* temp = top;
        char poppedValue = top->data;
        top = top->next;
        delete temp;
        return poppedValue;
    }

    char peek() {
        if (isEmpty()) return '\0';
        return top->data;
    }

    bool isEmpty() {
        return top == nullptr;
    }

    ~Stack() {
        while (!isEmpty()) pop();
    }
};

int precedence(char op) {
    if (op == '^')
        return 3;
    else if (op == '*' || op == '/')
        return 2;
    else if (op == '+' || op == '-')
        return 1;
    else
        return 0;
}

bool isOperator(char c) {
    return (c == '+' || c == '-' || c == '*' || c == '/' || c == '^');
}

string infixToPostfix(const string& infix) {
    Stack s;
    string postfix = "";

    for (char token : infix) {
        if (isalnum(token)) {
            postfix += token;
        } else if (token == '(') {
            s.push(token);
        } else if (token == ')') {
            while (!s.isEmpty() && s.peek() != '(') {
                postfix += s.pop();
            }
            s.pop();
        } else if (isOperator(token)) {
            while (!s.isEmpty() && precedence(s.peek()) >= precedence(token)) {
                postfix += s.pop();
            }
            s.push(token);
        }
    }

    while (!s.isEmpty()) {
        postfix += s.pop();
    }

    return postfix;
}

int main() {
    string infix;
    cout << "Enter infix expression: ";
    cin >> infix;

    string postfix = infixToPostfix(infix);
    cout << "\nPostfix Expression: " << postfix << endl;

    return 0;
}




Greedy Algorithm (Fractional Knapsack) –


#include <iostream>
#include <algorithm>
using namespace std;

struct Parcel {
    int id;
    double weight;
    double profit;
    double ratio;
};

bool compare(Parcel a, Parcel b) {
    return a.ratio > b.ratio;
}

void fractionalKnapsack(Parcel parcels[], int n, double capacity) {
    for (int i = 0; i < n; i++)
        parcels[i].ratio = parcels[i].profit / parcels[i].weight;

    sort(parcels, parcels + n, compare);

    double totalProfit = 0.0;
    double remainingCapacity = capacity;

    cout << "\n--- Parcel Selection Details ---\n";
    cout << "ID\tWeight\tProfit\tTaken\n";
    cout << "------------------------------------\n";

    for (int i = 0; i < n; i++) {
        if (parcels[i].weight <= remainingCapacity) {
            remainingCapacity -= parcels[i].weight;
            totalProfit += parcels[i].profit;
            cout << parcels[i].id << "\t" << parcels[i].weight << "\t"
                 << parcels[i].profit << "\t" << "100%" << endl;
        } else {
            double fraction = remainingCapacity / parcels[i].weight;
            totalProfit += parcels[i].profit * fraction;
            cout << parcels[i].id << "\t" << parcels[i].weight << "\t"
                 << parcels[i].profit << "\t" << fraction * 100 << "%" << endl;
            remainingCapacity = 0;
            break;
        }
    }

    cout << "------------------------------------\n";
    cout << "Maximum Profit = " << totalProfit << endl;
}

int main() {
    int n;
    double capacity;

    cout << "Enter number of parcels: ";
    cin >> n;

    Parcel* parcels = new Parcel[n];

    cout << "Enter parcel details (Weight and Profit):\n";
    for (int i = 0; i < n; i++) {
        parcels[i].id = i + 1;
        cout << "Parcel " << parcels[i].id << " Weight: ";
        cin >> parcels[i].weight;
        cout << "Parcel " << parcels[i].id << " Profit: ";
        cin >> parcels[i].profit;
    }

    cout << "Enter Truck Capacity: ";
    cin >> capacity;

    fractionalKnapsack(parcels, n, capacity);

    delete[] parcels;
    return 0;
}




5.
Implement Circular Queue using Array. Perform following operations on it. 
a) Insertion (Enqueue) 
b) Deletion (Dequeue) 
c) Display 
(Note: Handle queue full condition by considering a fixed size of a queue.) 

#include <iostream>
using namespace std;

#define SIZE 5

class CircularQueue {
private:
    int items[SIZE];
    int front, rear;

public:
    CircularQueue() {
        front = -1;
        rear = -1;
    }

    bool isFull() {
        return ((rear + 1) % SIZE == front);
    }

    bool isEmpty() {
        return (front == -1);
    }

    void enqueue(int value) {
        if (isFull()) {
            cout << "Queue is FULL! Cannot insert " << value << endl;
            return;
        }
        if (isEmpty()) {
            front = 0;
        }
        rear = (rear + 1) % SIZE;
        items[rear] = value;
        cout << "Inserted " << value << " into the queue.\n";
    }

    void dequeue() {
        if (isEmpty()) {
            cout << "Queue is EMPTY! Cannot delete.\n";
            return;
        }
        cout << "Deleted " << items[front] << " from the queue.\n";
        if (front == rear) {
            front = rear = -1;
        } else {
            front = (front + 1) % SIZE;
        }
    }

    void display() {
        if (isEmpty()) {
            cout << "Queue is EMPTY!\n";
            return;
        }
        cout << "Queue elements are: ";
        int i = front;
        while (true) {
            cout << items[i] << " ";
            if (i == rear)
                break;
            i = (i + 1) % SIZE;
        }
        cout << endl;
    }
};

int main() {
    CircularQueue q;
    int choice, value;

    cout << "------ CIRCULAR QUEUE IMPLEMENTATION ------\n";

    do {
        cout << "\n1. Enqueue\n2. Dequeue\n3. Display\n0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
        case 1:
            cout << "Enter value to insert: ";
            cin >> value;
            q.enqueue(value);
            break;
        case 2:
            q.dequeue();
            break;
        case 3:
            q.display();
            break;
        case 0:
            cout << "Exiting...\n";
            break;
        default:
            cout << "Invalid choice! Try again.\n";
        }
    } while (choice != 0);

    return 0;
}
OR
String Processing: Naïve String Matching –

#include <iostream>
#include <string>
using namespace std;

void naiveStringMatch(const string& text, const string& pattern) {
    int n = text.length();
    int m = pattern.length();
    bool found = false;

    cout << "\n--- Naïve String Matching ---\n";
    cout << "Text: \"" << text << "\"\n";
    cout << "Pattern: \"" << pattern << "\"\n\n";

    for (int i = 0; i <= n - m; i++) {
        int j;
        for (j = 0; j < m; j++) {
            if (text[i + j] != pattern[j])
                break;
        }
        if (j == m) {
            cout << "Pattern found at index: " << i << endl;
            found = true;
        }
    }

    if (!found)
        cout << "Pattern not found in the given text.\n";
}

int main() {
    string text, pattern;

    cout << "Enter the text: ";
    getline(cin, text);

    cout << "Enter the pattern to search: ";
    getline(cin, pattern);

    if (pattern.length() > text.length()) {
        cout << "\nError: Pattern length cannot exceed text length.\n";
        return 0;
    }

    naiveStringMatch(text, pattern);
    return 0;
}


Divide and Conquer (Merge Sort) – 

#include <iostream>
using namespace std;

void merge(int deliveryTimes[], int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;

    int* L = new int[n1];
    int* R = new int[n2];

    for (int i = 0; i < n1; i++)
        L[i] = deliveryTimes[left + i];
    for (int j = 0; j < n2; j++)
        R[j] = deliveryTimes[mid + 1 + j];

    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) {
            deliveryTimes[k] = L[i];
            i++;
        } else {
            deliveryTimes[k] = R[j];
            j++;
        }
        k++;
    }

    while (i < n1) {
        deliveryTimes[k] = L[i];
        i++; k++;
    }

    while (j < n2) {
        deliveryTimes[k] = R[j];
        j++; k++;
    }

    delete[] L;
    delete[] R;
}

void mergeSort(int deliveryTimes[], int left, int right) {
    if (left < right) {
        int mid = (left + right) / 2;
        mergeSort(deliveryTimes, left, mid);
        mergeSort(deliveryTimes, mid + 1, right);
        merge(deliveryTimes, left, mid, right);
    }
}

void display(int deliveryTimes[], int n) {
    for (int i = 0; i < n; i++)
        cout << deliveryTimes[i] << " ";
    cout << endl;
}

int main() {
    int n;
    cout << "Enter number of online orders: ";
    cin >> n;

    int* deliveryTimes = new int[n];

    cout << "Enter estimated delivery times (in minutes):\n";
    for (int i = 0; i < n; i++) {
        cout << "Order " << i + 1 << ": ";
        cin >> deliveryTimes[i];
    }

    cout << "\nBefore Sorting (Delivery Times): ";
    display(deliveryTimes, n);

    mergeSort(deliveryTimes, 0, n - 1);

    cout << "\nAfter Sorting (Prioritized Quickest Deliveries): ";
    display(deliveryTimes, n);

    delete[] deliveryTimes;
    return 0;
}





10.
Represent a graph of your college campus using adjacency list /adjacency matrix. Nodes should represent the various departments/institutes and links should represent the distance between them.  Find minimum spanning tree 
a) Using Kruskal’s algorithm. 

#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Edge {
    int src, dest, weight;
};

struct DisjointSet {
    vector<int> parent, rank;

    DisjointSet(int n) {
        parent.resize(n);
        rank.resize(n, 0);
        for (int i = 0; i < n; i++)
            parent[i] = i;
    }

    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);
        return parent[x];
    }

    void unionSets(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);

        if (rootX != rootY) {
            if (rank[rootX] < rank[rootY])
                parent[rootX] = rootY;
            else if (rank[rootX] > rank[rootY])
                parent[rootY] = rootX;
            else {
                parent[rootY] = rootX;
                rank[rootX]++;
            }
        }
    }
};

void kruskalMST(vector<Edge>& edges, int V) {
    vector<Edge> result;
    int totalWeight = 0;

    sort(edges.begin(), edges.end(), [](Edge a, Edge b) {
        return a.weight < b.weight;
    });

    DisjointSet ds(V);

    cout << "\nEdges in the Minimum Spanning Tree:\n";
    for (auto& edge : edges) {
        int root1 = ds.find(edge.src);
        int root2 = ds.find(edge.dest);

        if (root1 != root2) {
            result.push_back(edge);
            ds.unionSets(root1, root2);
            totalWeight += edge.weight;

            cout << "Department " << edge.src
                 << " -- " << edge.dest
                 << "  (Distance: " << edge.weight << ")\n";
        }
    }

    cout << "\nTotal Minimum Distance (MST Weight): " << totalWeight << " units\n";
}

int main() {
    int V = 6;
    vector<Edge> edges;

    cout << "College Campus Graph - Using Kruskal's Algorithm\n";
    cout << "\nDepartments:\n";
    cout << "0: Admin Block\n1: CSE Department\n2: Mechanical Department\n";
    cout << "3: Library\n4: Auditorium\n5: Hostel\n";

    edges = {
        {0, 1, 4},
        {0, 2, 6},
        {1, 2, 5},
        {1, 3, 3},
        {2, 3, 6},
        {3, 4, 2},
        {4, 5, 5},
        {2, 5, 7}
    };

    kruskalMST(edges, V);
    return 0;
}




Design a program  in cpp to maintain a student database that performs the following tasks: 
1. Add and store student details (ID, Name, CGPA) using dynamically allocated memory. 
2. Expand the student list using realloc() as new entries are added. 
3. Implement  Binary Search to find student records by ID. 
4. Implement Sorting Algorithms ( Selection Sort or Insertion Sort) to sort student records by: 
o Name (Alphabetically) 
o CGPA (Ascending/Descending) 



#include <iostream>
#include <cstring>
#include <cstdlib>
using namespace std;

struct Student {
    int id;
    char name[50];
    float cgpa;
};

void addStudent(Student*& students, int& count) {
    students = (Student*)realloc(students, (count + 1) * sizeof(Student));
    if (students == nullptr) {
        cout << "Memory allocation failed!\n";
        exit(1);
    }
    cout << "Enter ID: ";
    cin >> students[count].id;
    cin.ignore();
    cout << "Enter Name: ";
    cin.getline(students[count].name, 50);
    cout << "Enter CGPA: ";
    cin >> students[count].cgpa;
    count++;
}

void displayAll(Student* students, int count) {
    for (int i = 0; i < count; i++) {
        cout << "ID: " << students[i].id << ", Name: " << students[i].name << ", CGPA: " << students[i].cgpa << endl;
    }
}

void selectionSortByName(Student* students, int count) {
    for (int i = 0; i < count - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < count; j++) {
            if (strcmp(students[j].name, students[minIndex].name) < 0)
                minIndex = j;
        }
        swap(students[i], students[minIndex]);
    }
}

void insertionSortByCGPA(Student* students, int count, bool ascending = true) {
    for (int i = 1; i < count; i++) {
        Student key = students[i];
        int j = i - 1;
        while (j >= 0 && ((ascending && students[j].cgpa > key.cgpa) || (!ascending && students[j].cgpa < key.cgpa))) {
            students[j + 1] = students[j];
            j--;
        }
        students[j + 1] = key;
    }
}

int binarySearchByID(Student* students, int count, int id) {
    int left = 0, right = count - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (students[mid].id == id)
            return mid;
        else if (students[mid].id < id)
            left = mid + 1;
        else
            right = mid - 1;
    }
    return -1;
}

void sortByID(Student* students, int count) {
    for (int i = 0; i < count - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < count; j++) {
            if (students[j].id < students[minIndex].id)
                minIndex = j;
        }
        swap(students[i], students[minIndex]);
    }
}

int main() {
    Student* students = nullptr;
    int count = 0, choice;

    do {
        cout << "\n1. Add Student\n2. Display All\n3. Sort by Name\n4. Sort by CGPA Ascending\n5. Sort by CGPA Descending\n6. Search by ID\n0. Exit\n";
        cin >> choice;

        switch (choice) {
        case 1:
            addStudent(students, count);
            sortByID(students, count);
            break;
        case 2:
            displayAll(students, count);
            break;
        case 3:
            selectionSortByName(students, count);
            displayAll(students, count);
            break;
        case 4:
            insertionSortByCGPA(students, count, true);
            displayAll(students, count);
            break;
        case 5:
            insertionSortByCGPA(students, count, false);
            displayAll(students, count);
            break;
        case 6: {
            int id;
            cout << "Enter ID to search: ";
            cin >> id;
            int index = binarySearchByID(students, count, id);
            if (index != -1)
                cout << "Found: ID=" << students[index].id << ", Name=" << students[index].name << ", CGPA=" << students[index].cgpa << endl;
            else
                cout << "Record not found.\n";
            break;
        }
        case 0:
            cout << "Exiting...\n";
            break;
        default:
            cout << "Invalid choice.\n";
        }
    } while (choice != 0);

    free(students);
    return 0;
}
