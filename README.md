#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TRANSACTIONS 100
#define MAX_CATEGORY_LENGTH 20

// Structure to store a transaction
typedef struct {
    char date[11];      // Format: YYYY-MM-DD
    char category[MAX_CATEGORY_LENGTH];
    char type[10];      // "Income" or "Expense"
    float amount;
} Transaction;

Transaction transactions[MAX_TRANSACTIONS];
int transaction_count = 0;

// Function prototypes
void addTransaction();
void viewTransactions();
void monthlySummary();
void saveToFile();
void loadFromFile();
void searchByDate();

int main() {
    int choice;

    // Load existing transactions from file
    loadFromFile();

    do {
        printf("\n===== Personal Expense Tracker =====\n");
        printf("1. Add Transaction\n");
        printf("2. View Transactions\n");
        printf("3. Monthly Summary\n");
        printf("4. Search by Date\n");
        printf("5. Save & Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                addTransaction();
                break;
            case 2:
                viewTransactions();
                break;
            case 3:
                monthlySummary();
                break;
            case 4:
                searchByDate();
                break;
            case 5:
                saveToFile();
                printf("Data saved successfully. Exiting...\n");
                break;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    } while (choice != 5);

    return 0;
}

void addTransaction() {
    if (transaction_count >= MAX_TRANSACTIONS) {
        printf("Transaction limit reached!\n");
        return;
    }

    Transaction t;
    printf("Enter date (YYYY-MM-DD): ");
    scanf("%s", t.date);

    printf("Enter category (e.g., Food, Rent, etc.): ");
    scanf("%s", t.category);

    printf("Enter type (Income/Expense): ");
    scanf("%s", t.type);

    printf("Enter amount: ");
    scanf("%f", &t.amount);

    transactions[transaction_count++] = t;
    printf("Transaction added successfully!\n");
}

void viewTransactions() {
    if (transaction_count == 0) {
        printf("No transactions found!\n");
        return;
    }

    printf("\nDate       | Category       | Type      | Amount\n");
    printf("-----------------------------------------------\n");
    for (int i = 0; i < transaction_count; i++) {
        printf("%-10s | %-13s | %-8s | %.2f\n", 
               transactions[i].date, 
               transactions[i].category, 
               transactions[i].type, 
               transactions[i].amount);
    }
}

void monthlySummary() {
    float total_income = 0, total_expense = 0;

    for (int i = 0; i < transaction_count; i++) {
        if (strcmp(transactions[i].type, "Income") == 0) {
            total_income += transactions[i].amount;
        } else if (strcmp(transactions[i].type, "Expense") == 0) {
            total_expense += transactions[i].amount;
        }
    }

    printf("\n===== Monthly Summary =====\n");
    printf("Total Income: %.2f\n", total_income);
    printf("Total Expense: %.2f\n", total_expense);
    printf("Savings: %.2f\n", total_income - total_expense);
}

void saveToFile() {
    FILE *file = fopen("transactions.txt", "w");
    if (file == NULL) {
        printf("Error saving data!\n");
        return;
    }

    for (int i = 0; i < transaction_count; i++) {
        fprintf(file, "%s %s %s %.2f\n", 
                transactions[i].date, 
                transactions[i].category, 
                transactions[i].type, 
                transactions[i].amount);
    }

    fclose(file);
}

void loadFromFile() {
    FILE *file = fopen("transactions.txt", "r");
    if (file == NULL) {
        return; // No file to load
    }

    while (fscanf(file, "%s %s %s %f", 
                  transactions[transaction_count].date, 
                  transactions[transaction_count].category, 
                  transactions[transaction_count].type, 
                  &transactions[transaction_count].amount) != EOF) {
        transaction_count++;
    }

    fclose(file);
}

void searchByDate() {
    char date[11];
    printf("Enter date to search (YYYY-MM-DD): ");
    scanf("%s", date);

    int found = 0;
    printf("\nDate       | Category       | Type      | Amount\n");
    printf("-----------------------------------------------\n");
    for (int i = 0; i < transaction_count; i++) {
        if (strcmp(transactions[i].date, date) == 0) {
            printf("%-10s | %-13s | %-8s | %.2f\n", 
                   transactions[i].date, 
                   transactions[i].category, 
                   transactions[i].type, 
                   transactions[i].amount);
            found = 1;
        }
    }

    if (!found) {
        printf("No transactions found for the given date!\n");
    }
}
