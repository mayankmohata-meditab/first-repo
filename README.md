# first-repo
This is my first repo in meditab.
<br>CHANGED from locally.Changees done via remote.<br>again changed
#include <stdio.h>
#include <string.h>

/* --- Your existing definitions (assumed) --- */
#define MAX_TOKEN_LENGTH 50
#define NUM_TOKENS 4

char lora_parseData[NUM_TOKENS][MAX_TOKEN_LENGTH];

void send_event7(const char* key, const char* value) {
    // A dummy function for this example
    printf("Event: [%s] = [%s]\n", key, value);
}

void send_event7_tolora(const char* key, const char* value) {
    // A dummy function for this example
    printf("LoRa Event: [%s] = [%s]\n", key, value);
}
/* --- End of your definitions --- */


/**
 * Parses a string, splitting it on the first 3 commas.
 * The 4th token will contain everything remaining, including any other commas.
 */
int parse_string_by_3_commas(char* input_string) {

    char* start_of_token = input_string;
    char* end_of_token;
    int token_count = 0;
    
    // We loop 3 times to get the first 3 tokens (at index 0, 1, 2)
    for (token_count = 0; token_count < (NUM_TOKENS - 1); token_count++) {
        
        // Find the next comma
        end_of_token = strchr(start_of_token, ',');
        
        // Check if we found a comma
        if (end_of_token == NULL) {
            // Error: The string doesn't have 3 commas
            send_event7("Tokens", "less");
            send_event7_tolora("Tokens", "Not enough commas");
            return -1; // Indicate failure
        }
        
        // Calculate the length of this token
        int token_length = end_of_token - start_of_token;
        
        if (token_length >= MAX_TOKEN_LENGTH) {
            send_event7("Error", "Token too long");
            return -1;
        }

        // Copy the token (safely)
        strncpy(lora_parseData[token_count], start_of_token, token_length);
        lora_parseData[token_count][token_length] = '\0'; // Manually null-terminate
        
        send_event7("pd", lora_parseData[token_count]);
        
        // Move our 'start' pointer to the character *after* the comma
        start_of_token = end_of_token + 1;
    }
    
    // --- Get the 4th (and final) token ---
    // 'token_count' is now 3.
    // 'start_of_token' points to the beginning of the last token.
    // We take *everything* that is left.
    
    int last_token_length = strlen(start_of_token);
    
    if (last_token_length >= MAX_TOKEN_LENGTH) {
        send_event7("Error", "Last token too long");
        return -1;
    }

    // Copy the final token (everything remaining)
    strncpy(lora_parseData[token_count], start_of_token, MAX_TOKEN_LENGTH - 1);
    lora_parseData[token_count][MAX_TOKEN_LENGTH - 1] = '\0'; // Ensure termination
    
    send_event7("pd", lora_parseData[token_count]);

    return 0; // Success
}


// --- Main function to test it ---
int main() {
    
    // Your example: "abc,fcg,c,f"
    char test_string_1[] = "abc,fcg,c,f";
    printf("--- Parsing: [%s] ---\n", test_string_1);
    parse_string_by_3_commas(test_string_1);
    printf("\n");

    // Your other example: "1000,1,570,n"
    char test_string_2[] = "1000,1,570,n";
    printf("--- Parsing: [%s] ---\n", test_string_2);
    parse_string_by_3_commas(test_string_2);
    printf("\n");
    
    // A test to prove it works as requested
    char test_string_3[] = "my,data,here,and,the,rest,goes,here";
    printf("--- Parsing: [%s] ---\n", test_string_3);
    parse_string_by_3_commas(test_string_3);
    printf("\n");

    // A test for an error (not enough commas)
    char test_string_4[] = "not,enough";
    printf("--- Parsing: [%s] ---\n", test_string_4);
    parse_string_by_3_commas(test_string_4);
    printf("\n");
    
    return 0;
}
