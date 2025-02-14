# OR and AND gate using RISC-V (VSD Squadron mini)

### Overvie# OR & AND Gates using VSDQuadron Mini RISC-V Board  

## 📌 Project Overview  
This project implements **OR & AND logic gates** using the **VSDQuadron Mini RISC-V board**.  
It uses **GPIO pins** to take inputs from two **push buttons** and control two **LEDs** based on OR & AND logic.  

## 🛠 Components Required  
- **VSDQuadron Mini RISC-V Board**  
- **Two Push Buttons** (for inputs A & B)  
- **Two LEDs** (for OR & AND gate outputs)  
- **10kΩ Resistors** (for pull-down)  
- **330Ω Resistors** (for LEDs)  
- **Breadboard & Jumper Wires**  

## 🔌 Circuit Diagram  
 

## 💻 Code (logic_gates.c)  
```c
#include <stdio.h>
#include <wiringPi.h>

#define BUTTON_A 2
#define BUTTON_B 3
#define LED_OR   4
#define LED_AND  5

int main() {
    wiringPiSetup();
    pinMode(BUTTON_A, INPUT);
    pinMode(BUTTON_B, INPUT);
    pinMode(LED_OR, OUTPUT);
    pinMode(LED_AND, OUTPUT);

    while (1) {
        int A = digitalRead(BUTTON_A);
        int B = digitalRead(BUTTON_B);
        
        int or_result = A | B;
        int and_result = A & B;

        digitalWrite(LED_OR, or_result);
        digitalWrite(LED_AND, and_result);

        printf("A: %d, B: %d | OR: %d, AND: %d\n", A, B, or_result, and_result);
        delay(500);
    }

    return 0;
}
