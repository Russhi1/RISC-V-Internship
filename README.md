# SSSIHl_task1
 ### Week 1 

 We write a C program that adds number from 1 to 'n'.
* We first installed the leafpad editor if it was not previously installed.
* Then we write down our C code in the editor and save it as {filename.c} file.
  
![Screenshot 2024-12-21 102032](https://github.com/user-attachments/assets/21846f89-eb59-4c0e-ab57-71dd3e3b6755)

### Code

```
# include <stdio.h.>

int main() {
    int i, sum= 0, n= 5;
    for (i=o; i<=n; ++i) {
    sum +=i;
    }
    printf("Sum of the numbers from 1 to %d is %d\n", n,sum);
    return 0;
}
```

* After saving the file we write down the command
` gcc {filename.c} `
` ./a.out`
![Screenshot 2024-12-22 102848](https://github.com/user-attachments/assets/7a4cddf5-8042-4636-b80a-aee54b8d8722)


Then we can just play with the numbers to get different outputs.

* We write another command and run it.
` riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o {filename}.o {filename}.c`

![Screenshot 2024-12-22 103223](https://github.com/user-attachments/assets/2edc4b5e-4ac3-458d-b400-8eb22b31ad9a)

* We go to anothe tab and see the assembly code of the program

`riscv64-unknown-elf-objdump -d {filename}.o`


![Screenshot 2024-12-22 103339](https://github.com/user-attachments/assets/925c2d3d-04bb-4abf-8a7a-77bea3c374d1)

* Then we type another command

  ` riscv64-unknown-elf-objdump -d {filename}.o | less `

This will give us the sewction of the code but we require only the main section.
* Then we locate the byte address of the main section.

## Finding number of instructions

* We add the last instruction with 4 to get the next address.
  As it is byte instrucitons so it always increments by 4.

  ![Screenshot 2024-12-22 104541](https://github.com/user-attachments/assets/2537f112-2dac-462a-9f1a-2b18d332c352)

( The last instrcution is highlighted in blue)



### To count the number of instructions

* The first instruciton is subracted by the first instruction of the previous address and then divided by 4.


![Screenshot 2024-12-22 104733](https://github.com/user-attachments/assets/3ad492db-566b-4dff-a086-f0cfb9739b3a)


Then we get the number of instructions in the DEC section.



