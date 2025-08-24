#include <stdio.h>
#include <stdlib.h>
#include <string.h>
typedef struct {
    int studentID;
    char name[50];
    char department[50];
    float gpa;
} Student;
typedef struct Node {
    Student data;
    struct Node* next;
    struct Node* prev;
} Node;
Node* head = NULL;
Node* tail = NULL;
void insertRecord(int id, const char* name, const char* dept, float gpa);
void deleteRecordByID(int id);
void searchStudent(const char* query);
void displayAllRecords();
void reverseDisplayRecords();
Node* cloneList();
void calculateAverageGPA();
void freeList(Node* startNode);

int main() {
    int choice, id, deleteId;
    char name[50], dept[50], searchQuery[50];
    float gpa;

    printf("--- Student Record Management System ---\n");

    while (1) {
        printf("\nMenu:\n");
        printf("1. Insert new student record\n");
        printf("2. Delete a student record by ID\n");
        printf("3. Search for a student by name or ID\n");
        printf("4. Display all records\n");
        printf("5. Reverse display of records\n");
        printf("6. Clone the list (for backup)\n");
        printf("7. Calculate average GPA\n");
        printf("8. Exit\n");
        printf("Enter your choice: ");
        
        if (scanf("%d", &choice) != 1) {
            printf("Invalid input. Please enter a number.\n");
            continue;
        }
      

        switch (choice) {
            case 1:
                printf("Enter Student ID: ");
                scanf("%d", &id);
                clearInputBuffer();
                printf("Enter Name: ");
                fgets(name, 50, stdin);
                name[strcspn(name, "\n")] = 0; // Remove trailing newline
                printf("Enter Department: ");
                fgets(dept, 50, stdin);
                dept[strcspn(dept, "\n")] = 0; // Remove trailing newline
                printf("Enter GPA: ");
                scanf("%f", &gpa);
                clearInputBuffer();
                insertRecord(id, name, dept, gpa);
                printf("Record inserted successfully.\n");
                break;
            case 2:
                printf("Enter Student ID to delete: ");
                scanf("%d", &deleteId);
                deleteRecordByID(deleteId);
                break;
            case 3:
                printf("Enter Name or ID to search: ");
                fgets(searchQuery, 50, stdin);
                searchQuery[strcspn(searchQuery, "\n")] = 0;
                searchStudent(searchQuery);
                break;
            case 4:
                displayAllRecords();
                break;
            case 5:
                reverseDisplayRecords();
                break;
            case 6:
                printf("Cloning list...\n");
                Node* clonedHead = cloneList();
                if (clonedHead) {
                    printf("List cloned successfully.\n");
                    freeList(clonedHead); // Free cloned list memory
                }
                break;
            case 7:
                calculateAverageGPA();
                break;
            case 8:
                printf("Exiting program. Freeing memory...\n");
                freeList(head);
                return 0;
            default:
                printf("Invalid choice. Please try again.\n");
                break;
        }
    }
    return 0;
}
void insertRecord(int id, const char* name, const char* dept, float gpa) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Memory allocation failed!\n");
        return;
    }

    newNode->data.studentID = id;
    strcpy(newNode->data.name, name);
    strcpy(newNode->data.department, dept);
    newNode->data.gpa = gpa;
    newNode->next = NULL;
    newNode->prev = NULL;

    if (head == NULL) {
        head = newNode;
        tail = newNode;
    } else {
        tail->next = newNode;
        newNode->prev = tail;
        tail = newNode;
    }
}
void deleteRecordByID(int id) {
    Node* current = head;
    while (current != NULL && current->data.studentID != id) {
        current = current->next;
    }

    if (current == NULL) {
        printf("Student with ID %d not found.\n", id);
        return;
    }

    if (current->prev != NULL) {
        current->prev->next = current->next;
    } else {
        head = current->next;
    }

    if (current->next != NULL) {
        current->next->prev = current->prev;
    } else {
        tail = current->prev;
    }

    free(current);
    printf("Student with ID %d deleted successfully.\n", id);
}


void searchStudent(const char* query) {
    Node* current = head;
    int found = 0;
    while (current != NULL) {
        char idStr[20];
        sprintf(idStr, "%d", current->data.studentID);

        if (strcmp(current->data.name, query) == 0 || strcmp(idStr, query) == 0) {
            printf("Found: ID: %d, Name: %s, Dept: %s, GPA: %.2f\n",
                   current->data.studentID, current->data.name, current->data.department, current->data.gpa);
            found = 1;
        }
        current = current->next;
    }
    if (!found) {
        printf("No student found matching the query '%s'.\n", query);
    }
}


void displayAllRecords() {
    Node* current = head;
    if (current == NULL) {
        printf("No records to display.\n");
        return;
    }
    printf("--- Student Records (Order of Entry) ---\n");
    while (current != NULL) {
        printf("ID: %d, Name: %s, Dept: %s, GPA: %.2f\n",
               current->data.studentID, current->data.name, current->data.department, current->data.gpa);
        current = current->next;
    }
    printf("---------------------------------------\n");
}


void reverseDisplayRecords() {
    Node* current = tail;
    if (current == NULL) {
        printf("No records to display.\n");
        return;
    }
    printf("--- Student Records (Reverse Order) ---\n");
    while (current != NULL) {
        printf("ID: %d, Name: %s, Dept: %s, GPA: %.2f\n",
               current->data.studentID, current->data.name, current->data.department, current->data.gpa);
        current = current->prev;
    }
    printf("---------------------------------------\n");
}

/**
 * Clones the entire list and returns a pointer to the head of the new list.
 */
Node* cloneList() {
    if (head == NULL) {
        return NULL;
    }

    Node* newHead = NULL;
    Node* newTail = NULL;
    Node* current = head;

    while (current != NULL) {
        Node* newNode = (Node*)malloc(sizeof(Node));
        if (newNode == NULL) {
            printf("Memory allocation failed during cloning!\n");
            freeList(newHead);
            return NULL;
        }
        newNode->data = current->data;
        newNode->next = NULL;
        newNode->prev = NULL;

        if (newHead == NULL) {
            newHead = newNode;
            newTail = newNode;
        } else {
            newTail->next = newNode;
            newNode->prev = newTail;
            newTail = newNode;
        }
        current = current->next;
    }
    return newHead;}
}
void calculateAverageGPA() {
    Node* current = head;
    if (current == NULL) {
        printf("No records to calculate GPA.\n");
        return;
    }

    float totalGPA = 0.0;
    int count = 0;
    while (current != NULL) {
        totalGPA += current->data.gpa;
        count++;
        current = current->next;
    }

    if (count > 0) {
        float averageGPA = totalGPA / count;
        printf("Average GPA for all students: %.2f\n", averageGPA);
    } else {
        printf("No students found to calculate average GPA.\n");
    }
}
void freeList(Node* startNode) {
    Node* current = startNode;
    Node* nextNode;
    while (current != NULL) {
        nextNode = current->next;
        free(current);
        current = nextNode;
    }
}
