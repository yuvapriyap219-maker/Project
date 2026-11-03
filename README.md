## Ex. 3a – Stop and Wait Protocol

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main(void)
{
    int n, i;

    srand((unsigned)time(NULL));
    printf("Enter number of frames: ");
    scanf("%d", &n);
    printf("\n");

    for (i = 1; i <= n; i++) {
        printf("Sending frame %d...\n", i);
        if (rand() % 2 == 0)                       /* simulate a lost frame */
            printf("Frame %d lost! Resending...\n", i);
        printf("Acknowledgment for frame %d received.\n", i);
    }

    printf("\nAll frames sent successfully (Stop and Wait ARQ)\n");
    printf("Press Enter to exit...");
    getchar();
    getchar();
    return 0;
}
```

## Ex. 3b – Sliding Window Protocol

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define WINDOW 4

int main(void)
{
    int total, base = 1, i, end;

    srand((unsigned)time(NULL));
    printf("Enter total number of frames: ");
    scanf("%d", &total);
    printf("\n");

    while (base <= total) {
        end = (base + WINDOW - 1 < total) ? base + WINDOW - 1 : total;

        printf("Sending frames:");
        for (i = base; i <= end; i++)
            printf(" %d", i);
        printf("\n");

        if (rand() % 2 == 0) {                     /* window lost -> resend */
            printf("Some frames lost! Resending...\n");
            printf("Sending frames:");
            for (i = base; i <= end; i++)
                printf(" %d", i);
            printf("\n");
        }

        printf("Acknowledgment received up to frame %d\n", end);
        base = end + 1;
    }

    printf("\nAll frames sent successfully (Sliding Window ARQ)\n");
    return 0;
}
```

## Ex. 4a – Selective Repeat Protocol

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define WINDOW 4

int main(void)
{
    int total, base = 1, i, end, lost;

    srand((unsigned)time(NULL));
    printf("Enter total number of frames: ");
    scanf("%d", &total);
    printf("\n");

    while (base <= total) {
        end = (base + WINDOW - 1 < total) ? base + WINDOW - 1 : total;

        printf("Sending frames:");
        for (i = base; i <= end; i++)
            printf(" %d", i);
        printf("\n");

        if (rand() % 2 == 0) {                     /* one frame in the window is lost */
            lost = base + rand() % (end - base + 1);
            printf("No acknowledgement for frame %d\n", lost);
            printf("Retransmitting frame %d...\n", lost);
            printf("Sending frames:");
            for (i = base; i <= end; i++)
                printf(" %d", i);
            printf("\n");
        }

        printf("Acknowledgement received up to frame %d\n", end);
        base = end + 1;
    }

    printf("\nAll frames sent successfully (Selective Repeat ARQ)\n");
    return 0;
}
```

## Ex. 4b – Go-Back-N Protocol

```c
#include <stdio.h>

int main(void)
{
    int total, N, base = 1, end, i, ack, sent = 0;

    printf("--- Go-Back-N Protocol Simulation ---\n");
    printf("Enter the total number of frames to send: ");
    scanf("%d", &total);
    printf("Enter the Window Size (N): ");
    scanf("%d", &N);

    printf("\nStarting transmission...\n");
    printf("========================================\n");

    while (base <= total) {
        end = (base + N - 1 < total) ? base + N - 1 : total;

        printf("\n[Sender] Sending Window:");
        for (i = base; i <= end; i++) {
            printf(" %d", i);
            sent++;
        }
        printf("\n");

        printf("[Receiver] Enter the last successfully received frame (Expected: %d): ", base);
        scanf("%d", &ack);

        if (ack >= base && ack <= end) {
            printf("--> ACK %d received. Sliding window forward.\n", ack);
            base = ack + 1;
        } else {
            printf("--> Timeout/Error! Go Back N: Retransmitting from frame %d\n", base);
        }
    }

    printf("========================================\n");
    printf("All %d frames sent successfully!\n", total);
    printf("Total frame transmissions attempted: %d\n", sent);
    return 0;
}
```

## Ex. 8 – Leaky Bucket Algorithm

```python
import random
import matplotlib.pyplot as plt

bucket_capacity = 10
leak_rate = 2
simulation_time = 20
bucket_content = 0
packets_dropped = 0
packets_sent = 0

time_points = []
incoming_packets = []
dropped_packets = []
bucket_sizes = []

for t in range(simulation_time):
    incoming = random.randint(0, 5)  # bursty incoming packets
    space_left = bucket_capacity - bucket_content

    if incoming <= space_left:
        bucket_content += incoming
    else:
        packets_dropped += (incoming - space_left)
        bucket_content = bucket_capacity

    leak = min(leak_rate, bucket_content)
    bucket_content -= leak
    packets_sent += leak

    time_points.append(t)
    incoming_packets.append(incoming)
    dropped_packets.append(packets_dropped)
    bucket_sizes.append(bucket_content)

    print(f"Time {t}s: Incoming={incoming}, Bucket={bucket_content}, Dropped={packets_dropped}")

# Plotting the results
plt.plot(time_points, incoming_packets, label='Incoming Packets')
plt.plot(time_points, bucket_sizes, label='Bucket Content')
plt.plot(time_points, dropped_packets, label='Packets Dropped')
plt.xlabel('Time (s)')
plt.ylabel('Packets')
plt.title('Leaky Bucket Congestion Control')
plt.legend()
plt.grid(True)
plt.show()
```

## Ex. 9 – Error Detection and Correction (Hamming Code)

```c
#include <stdio.h>

/* Hamming (7,4) code, bit order (positions 1..7): p1 p2 d1 p3 d2 d3 d4 */
int main(void)
{
    int d[4], code[8], r[8], i, p1, p2, p3, pos;

    printf("Enter 4 data bits:\n");
    for (i = 0; i < 4; i++)
        scanf("%d", &d[i]);

    code[3] = d[0]; code[5] = d[1]; code[6] = d[2]; code[7] = d[3];
    code[1] = code[3] ^ code[5] ^ code[7];
    code[2] = code[3] ^ code[6] ^ code[7];
    code[4] = code[5] ^ code[6] ^ code[7];

    printf("Hamming code: ");
    for (i = 1; i <= 7; i++)
        printf("%d", code[i]);
    printf("\n");

    printf("Enter received 7 bits:\n");
    for (i = 1; i <= 7; i++)
        scanf("%d", &r[i]);

    p1 = r[1] ^ r[3] ^ r[5] ^ r[7];
    p2 = r[2] ^ r[3] ^ r[6] ^ r[7];
    p3 = r[4] ^ r[5] ^ r[6] ^ r[7];
    pos = p3 * 4 + p2 * 2 + p1;

    printf("\n");
    if (pos == 0) {
        printf("No error. Received code is correct.\n");
    } else {
        printf("Error at position %d\n", pos);
        r[pos] ^= 1;
        printf("Corrected code: ");
        for (i = 1; i <= 7; i++)
            printf("%d", r[i]);
        printf("\n");
    }
    return 0;
}
```

## Ex. 10a – Encryption

```c
#include <stdio.h>

int main(void)
{
    FILE *fp1, *fp2;
    char ch;

    fp1 = fopen("message.txt", "r");
    if (fp1 == NULL) {
        printf("Source File Could Not Be Found\n");
        return 1;
    }
    fp2 = fopen("encrypted.txt", "w");
    if (fp2 == NULL) {
        printf("Target File Could Not Be Found\n");
        fclose(fp1);
        return 1;
    }

    while ((ch = fgetc(fp1)) != EOF)
        fputc(ch - 3, fp2);          /* shift every character back by 3 */

    fclose(fp1);
    fclose(fp2);
    printf("File encrypted successfully.\n");
    return 0;
}
```

## Ex. 10b – Decryption

```c
#include <stdio.h>

int main(void)
{
    FILE *fp1, *fp2;
    char ch;

    fp1 = fopen("encrypted.txt", "r");
    if (fp1 == NULL) {
        printf("File 1 Not Found\n");
        return 1;
    }
    fp2 = fopen("decrypted.txt", "w");
    if (fp2 == NULL) {
        printf("File 2 Not Found\n");
        fclose(fp1);
        return 1;
    }

    while ((ch = fgetc(fp1)) != EOF)
        fputc(ch + 3, fp2);          /* shift every character forward by 3 */

    fclose(fp1);
    fclose(fp2);
    printf("File Decrypted Successfully\n");
    return 0;
}
```
