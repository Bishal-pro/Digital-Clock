# Digital-Clock
/*
    DIGITAL CLOCK PROJECT IN C
    Features:
      1. Digital Clock
      2. Stopwatch
      3. Alarm
      4. World Clock

    FRONT END:  Menus + user interaction (main(), showMenu(), etc.)
    BACK END :  Time calculations, stopwatch, alarm, world clock logic
*/

#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#ifdef _WIN32
    #include <windows.h>
    #define SLEEP(seconds) Sleep((seconds) * 1000)
#else
    #include <unistd.h>
    #define SLEEP(seconds) sleep(seconds)
#endif

/* -------------------- BACK-END DATA STRUCTURES -------------------- */

typedef struct {
    int hour;    // 0-23
    int minute;  // 0-59
    int enabled; // 1 = ON, 0 = OFF
} Alarm;

Alarm alarmData = {0, 0, 0};  // global alarm

/* ----------------------- BACK-END FUNCTIONS ----------------------- */

// Get current local time and print as HH:MM:SS
void printCurrentTime() {
    time_t now = time(NULL);
    struct tm *local = localtime(&now);

    printf("%02d:%02d:%02d\n",
           local->tm_hour,
           local->tm_min,
           local->tm_sec);
}

// Simple digital clock that runs for given number of seconds
void digitalClock() {
    int seconds;
    printf("\n--- DIGITAL CLOCK ---\n");
    printf("How many seconds do you want to run the clock? ");
    scanf("%d", &seconds);

    printf("Clock running... (HH:MM:SS)\n");
    for (int i = 0; i < seconds; i++) {
        system("cls||clear");  // clear screen for Windows or Linux
        printf("DIGITAL CLOCK\n");
        printCurrentTime();
        SLEEP(1);
    }
    printf("\nClock finished. Returning to main menu...\n");
    SLEEP(2);
}

// Stopwatch: counts up until user gives a limit (seconds)
void stopwatch() {
    int limit;
    printf("\n--- STOPWATCH ---\n");
    printf("Enter duration in seconds (how long to run stopwatch): ");
    scanf("%d", &limit);

    printf("Stopwatch started...\n");
    for (int elapsed = 0; elapsed <= limit; elapsed++) {
        system("cls||clear");
        printf("STOPWATCH\n");
        printf("Elapsed time: %02d:%02d:%02d\n",
               elapsed / 3600,
               (elapsed % 3600) / 60,
               elapsed % 60);
        SLEEP(1);
    }
    printf("\nStopwatch finished. Returning to main menu...\n");
    SLEEP(2);
}

// Set alarm time
void setAlarm() {
    int h, m;
    printf("\n--- SET ALARM ---\n");
    printf("Enter alarm time (24-hour format).\n");
    printf("Hour (0-23): ");
    scanf("%d", &h);
    printf("Minute (0-59): ");
    scanf("%d", &m);

    if (h < 0 || h > 23 || m < 0 || m > 59) {
        printf("Invalid time. Alarm not set.\n");
        SLEEP(2);
        return;
    }

    alarmData.hour = h;
    alarmData.minute = m;
    alarmData.enabled = 1;

    printf("\nAlarm set for %02d:%02d.\n", h, m);
    printf("NOTE: This function will wait until the alarm time and then ring.\n");
    printf("Do not close the program until it rings.\n");
    SLEEP(3);

    printf("\nWaiting for alarm...\n");
    while (1) {
        time_t now = time(NULL);
        struct tm *local = localtime(&now);

        if (local->tm_hour == alarmData.hour &&
            local->tm_min  == alarmData.minute) {
            break;
        }
        SLEEP(10); // check every 10 seconds
    }

    // Alarm rings
    system("cls||clear");
    printf("*\n");
    printf("*        ALARM RINGING!   *\n");
    printf("*\n");
    for (int i = 0; i < 5; i++) {
        printf("\a"); // beep sound (may depend on system)
        SLEEP(1);
    }

    alarmData.enabled = 0; // disable after one ring
    printf("\nAlarm finished. Returning to main menu...\n");
    SLEEP(3);
}

// Print time of a city using offset from UTC
void printCityTime(const char *city, int offsetHours) {
    time_t now = time(NULL);

    // use UTC as base
    time_t utc = now;  // time() is in seconds since epoch (UTC)
    utc += offsetHours * 3600;

    struct tm *tm_info = gmtime(&utc);

    printf("%-15s : %02d:%02d:%02d\n",
           city,
           tm_info->tm_hour,
           tm_info->tm_min,
           tm_info->tm_sec);
}

// World clock: show time in some major cities
void worldClock() {
    printf("\n--- WORLD CLOCK ---\n");
    printf("Showing times of some cities based on UTC:\n\n");
    /*
        Offsets (approx, no DST):
          London   : UTC+0
          New York : UTC-5
          Dubai    : UTC+4
          India    : UTC+5:30 (we only use hours here → +5)
          Tokyo    : UTC+9
    */
    for (int i = 0; i < 5; i++) {
        system("cls||clear");
        printf("WORLD CLOCK (refreshing every second)\n\n");

        printCityTime("London (UTC)",       0);
        printCityTime("New York",         -5);
        printCityTime("Dubai",             4);
        printCityTime("India (approx)",    5);  // approx, no 30 min
        printCityTime("Tokyo",             9);

        SLEEP(1);
    }
    printf("\nWorld clock demo finished. Returning to main menu...\n");
    SLEEP(3);
}

/* ----------------------- FRONT-END FUNCTIONS ---------------------- */

void showMenu() {
    system("cls||clear");
    printf("=========================================\n");
    printf("        DIGITAL CLOCK APPLICATION        \n");
    printf("=========================================\n");
    printf("1. Digital Clock\n");
    printf("2. Stopwatch\n");
    printf("3. Set Alarm\n");
    printf("4. World Clock\n");
    printf("5. Exit\n");
    printf("Enter your choice: ");
}

/* --------------------------- MAIN (UI) ---------------------------- */

int main() {
    int choice;

    while (1) {
        showMenu();
        if (scanf("%d", &choice) != 1) {
            // handle invalid input
            printf("Invalid input. Exiting...\n");
            break;
        }

        switch (choice) {
        case 1:
            digitalClock();
            break;
        case 2:
            stopwatch();
            break;
        case 3:
            setAlarm();
            break;
        case 4:
            worldClock();
            break;
        case 5:
            printf("Exiting program. Goodbye!\n");
            exit(0);
        default:
            printf("Invalid choice. Please try again.\n");
            SLEEP(2);
        }
    }

    return 0;
}
