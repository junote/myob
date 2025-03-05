
### printf_flush

```

#include <stdio.h>
#include <unistd.h> // For sleep function

int main() {
    int i;
    for (i = 0; i <= 100; i++) {
        printf("\rProgress: %d%%", i);
        fflush(stdout); // Ensure the output is printed immediately
        sleep(1); // Sleep for 1 second
    }
    printf("\n"); // Move to the next line after the loop
    return 0;
}

```

