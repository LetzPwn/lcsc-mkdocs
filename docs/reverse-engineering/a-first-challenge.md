# A first challenge

Let's get straight into an example challenge to showcase what the reverse engineering category is about.

This challenge is called "Who Wants to be a Millionaire: The Oracle's Whimsy".
The description says:
> In a future where reality has become unstable, an enigmatic entity known as The Oracle has grown bored with logic and created a game show where nothing makes sense. In Who Wants to be a Millionaire: The Oracle's Whimsy, contestants face absurd, gibberish questions with answers that defy reason, relying solely on luck to win unimaginable riches. Knowledge is obsolete, and the only way to succeed is by pleasing The Oracle through random guesses. Audiences are captivated by the sheer chaos and unpredictability, as players navigate a world where the only rule is that there are no rules. In this twisted game, wealth is determined not by wisdom, but by chance.

**DOWNLOAD**: The challenge provides the file `the_oracles_whimsy` which can be downloaded [here](/challenge-files/the_oracles_whimsy).

## First inspections

First thing we do is open the terminal and run the command `file` on `the_oracles_whimsy` to give some basic information about the file.

```bash
$ file the_oracles_whimsy
the_oracles_whimsy: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=cf4e3000306a57f52e6e3ba993722d58fb248121, for GNU/Linux 3.2.0, stripped
```

The output of the command tells us a lot of things. Most important are the following

- ELF: The file is an [ELF](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) file. ELF stands for Executable and Linkable Format and is a common standard file format for executable files, object code, shared libraries, and core dumps.
- 64-bit: The program is compiled for 64-bit architecture. This means for example that the adresses have a length of 64-bit.
- LSB: LSB stands for Least Significant Byte and specifies the order of the bytes/ the [endianness](https://en.wikipedia.org/wiki/Endianness) used.
- x86-64: this specifies the instruction set (assembly). Alternatives to x86-64 would be for example "Intel 80386" or "ARM".
- stripped: this means that symbols, like function names or global variables names have been deleted from the binary as some sort of [obfuscation](https://en.wikipedia.org/wiki/Obfuscation_(software)).

## Running the binary

A word of caution: We do run this code, because we know that it is a harmless ctf challenge and is not going to do anything malicious to our machine. Do not run programs that you don't trust before inspecting it statically first. If you decide to run a program, always do so in a dedicated virtual machine and not on your host.

To run the binary, we first have to adjust the files permission to make it executable by setting the "executable bit" with the command `chmod`.

```bash
$ chmod +x the_oracles_whimsy
```

Then, we are able to run it.

```
$ ./the_oracles_whimsy
Welcome to: 

╔╦╗┬ ┬┌─┐  ╔═╗┬─┐┌─┐┌─┐┬  ┌─┐┌─┐  ╦ ╦┬ ┬┬┌┬┐┌─┐┬ ┬
 ║ ├─┤├┤   ║ ║├┬┘├─┤│  │  ├┤ └─┐  ║║║├─┤││││└─┐└┬┘
 ╩ ┴ ┴└─┘  ╚═╝┴└─┴ ┴└─┘┴─┘└─┘└─┘  ╚╩╝┴ ┴┴┴ ┴└─┘ ┴ 


The rules are: 
  1) All 15 questions are nonsensical and have no logical answers.
  2) Answer the questions by writing 'A', 'B', 'C', or 'D'.
  3) Win by guessing all 15 questions right.
  4) Cryptic Price: The winner receives a mysterious reward!

Ready or not, the game starts now! Good luck! 


50€: What flavor does the wind have when it whispers secrets to the ocean?
A: 3.14159265                    B: yikes
C: Charlie Tango Foxtrot         D: Frog

Your answer: 

```

In this case it is, as expected, a kind of version like "Who wants to be a millionaire" but with nonsense questions and answers. When running the program a second time, we notice that the question and the answers change.
To get this "mysterious reward" (the flag most probably), we would have to guess the right answer 15 times in a row. Chance of doing that is `0.25 ^ 15 = 0,000000000931323`, so we don't want to play this game the normal game.

## How to cheat the game
 
We want to find a way to cheat in the app. Take a minute to think about what you think would be possible to do. There are a lot of ways possible and I want to showcase some different ideas.

A non-exhaustive list of ways/ideas to cheat

- decompile the app and try to find the flag inside the app
- run the app in a debugger. there you can change the values of registers manually and jump right to after the 15th question.
- run the app in a debugger and stop at every point, where your answer is compared to the "right" answer and flip the bit to true.
- patch the binary to jump to outputting the flag rigth away.
- the binary is probably using the "rand" function to choose the questions, answers and each right answer. override this random function with an own implementation that always returns the same value


## Decompiling with ghidra

When first opening ghidra, you have to create a new Project with `File > New Project` from the menu bar, select "Non-Shared Project" and give it a name.
After creating the new project, include the binary "the_oracles_whimsy" by clicking `File > Import File...` and selecting the binary and clicking "OK" twice..

Now, the binary shows up in the Tree View of the project. Double-click it or drag it on the dragon-icon.
ghidra will ask, if you want to analyze the binary. Click "Yes" and "Analyze".

As this binary is stripped, we first need to find the main function by hand. In the panel "Symbol tree" on the left, search for the "entry" function and select it.

The following will show up in the decompilation pane on the right:

```c
void processEntry entry(undefined8 param_1,undefined8 param_2)

{
  undefined auStack_8 [8];
  
  __libc_start_main(FUN_001012a2,param_2,&stack0x00000008,0,0,param_1,auStack_8);
  do {
                    /* WARNING: Do nothing block with infinite loop */
  } while( true );
}
```

The first argument to `__libc_start_main` is always a pointer to the main function. Click on `FUN_001012a2` and press l to rename the function to "main". Now double-click `main` to jump to the decompiled main function.

```c
undefined8 main(void)

{
  byte bVar1;
  int iVar2;
  time_t tVar3;
  char local_32;
  char local_31;
  undefined *local_30;
  undefined *local_28;
  undefined *local_20;
  undefined *local_18;
  int local_c;
  
  tVar3 = time((time_t *)0x0);
  srand((uint)tVar3);
  puts("Welcome to: \n");
  puts(&DAT_00103780);
  puts(&DAT_00103808);
  puts(&DAT_00103888);
  puts("The rules are: ");
  puts("  1) All 15 questions are nonsensical and have no logical answers.");
  puts("  2) Answer the questions by writing \'A\', \'B\', \'C\', or \'D\'.");
  puts("  3) Win by guessing all 15 questions right.");
  puts("  4) Cryptic Price: The winner receives a mysterious reward!\n");
  puts("Ready or not, the game starts now! Good luck! \n\n");
  local_c = 0;
  while( true ) {
    if (0xe < local_c) {
      FUN_00101199();
      return 0;
    }
    printf(&DAT_00103a49,(ulong)*(uint *)(&DAT_00103700 + (long)local_c * 4),
           (&PTR_s_What_flavor_does_the_wind_have_w_00106060)[local_c]);
    iVar2 = rand();
    local_18 = (&PTR_s_Snergle_00106100)[(ulong)(long)iVar2 % 0x28];
    iVar2 = rand();
    local_20 = (&PTR_s_Snergle_00106100)[(ulong)(long)iVar2 % 0x28];
    iVar2 = rand();
    local_28 = (&PTR_s_Snergle_00106100)[(ulong)(long)iVar2 % 0x28];
    iVar2 = rand();
    local_30 = (&PTR_s_Snergle_00106100)[(ulong)(long)iVar2 % 0x28];
    iVar2 = rand();
    bVar1 = (byte)(iVar2 >> 0x1f);
    local_31 = (((char)iVar2 + (bVar1 >> 6) & 3) - (bVar1 >> 6)) + 'A';
    printf("A: %-22s\t B: %s\n",local_18,local_20);
    printf("C: %-22s\t D: %s\n",local_28,local_30);
    putchar(10);
    printf("Your answer: ");
    __isoc99_scanf(&DAT_00103a84,&local_32);
    if (local_31 != local_32) break;
    puts("Good job, that was the right answer! ");
    local_c = local_c + 1;
  }
  printf("I\'m sorry, that\'s not correct. The answer the Oracle was looking for is \'%c\'. You lose !\n"
         ,(ulong)(uint)(int)local_31);
  return 0;
}
```

This is the C Code of the main function that ghidra decompiled from the binary code. Now we can try to make sense of what exactly is happening.
Decompiled code in ghidra always starts with the declaration of all the local variables in the function.
The following two lines are:
```c
  tVar3 = time((time_t *)0x0);
  srand((uint)tVar3);
```
This sets up a pseudo-random number generator and seeds it with the current time.
`srand` is an indication that later on, the `rand` function is being used to generate random values.

Next, the rules are being printed to the console via the `puts` function.

Then comes a `while(True)` Loop.
Here it makes sense to look, how the program can break out of the loop and find the following:
```c
local_c = 0;
while( true ) {
  if (0xe < local_c) {
    FUN_00101199();
    return 0;
  }
  ...
  if (local_31 != local_32) break;
  ...
  local_c = local_c + 1;
}
```
We see the condition `if(0xe < local_c)`. `0xe` represents the number 14 in hexadecimal number representation. You can rightclick on the number in ghidra to see the values in decimal, binary, octal and other representations.
This means, if the variable `local_c`, that was initialised to 0 before the loop, is greater than 14, the function `FUN_00101199` is called and the main function ends with the `return 0;` statement. The variable `local_c` is incremented each iteration. If we think of the rules, we notice that there are 15 questions. So this `local_c` might represent the number of the current question. Let's rename the variable to `question_nbr` by hitting `l` on the keyboard.

As an interesting side-note, the original program could also have looked like this:
```c
for (int i = 0; i < 15; i++){
  ...
  if (local_31 != local_32) break;
}
FUN_00101199();
return 0;
```
This does exactly the same as the while loop from above and results in the same compiled binary code. Some decompilers might decompile it to a while loop, while other decompile it to a for loop. You can use [dogbolt.org](https://dogbolt.org/) to see what different decompilers do. In our example, the Hex-Rays decompiler shows a for-loop, while ghidra and binaryninja both show a while-loop.

Now, let's look into `FUN_00101199`, which is most probably the `win` or `getflag` function, whatever you would like to call it.

```c
void FUN_00101199(void)

{
  undefined8 local_118;
  undefined8 local_110;
  undefined8 local_108;
  undefined8 local_100;
  undefined8 local_f8;
  undefined8 local_f0;
  undefined8 local_e8;
  undefined8 local_e0;
  undefined local_d8 [204];
  int local_c;
  
  local_e8 = 0x7766554433221100;
  local_e0 = 0xffeeddccbbaa9988;
  local_118 = 0xac1d60871fc5bd37;
  local_110 = 0xa25f61a4f747edfd;
  local_108 = 0x591d872197736fb7;
  local_100 = 0x6c3550e8e7a13305;
  local_f8 = 0x2035325aab697d40;
  local_f0 = 0x68dd201dde4edda4;
  FUN_00101832(local_d8,&local_e8);
  for (local_c = 0; local_c < 3; local_c = local_c + 1) {
    FUN_0010270c(local_d8,(long)&local_118 + (long)(local_c << 4));
  }
  printf("Congrats, you win! Here is your mystery rewards: ");
  puts((char *)&local_118);
  return;
}
```

We see: At the bottom, there is a message printed "Congrats, you win! Here is your mystery rewards:" and the value at pointer `local_118`, that is the flag. We also see that this value is "calculated" beforehand using other functions `FUN_00101832` and `FUN_0010270c`. At this point, we kinda don't want to spend more time looking at this code statically and trying to make sense of more stuff.

In the outgoing calls we even see, that a lot more nested functions are being called.
![image](/data/rev_outgoing_references.png)

So let's jump into our Dynamic Analysis!

## Dynamic analysis with gdb+gef

Make sure to have gdb and gef (GDB enhanced features) installed.

```
gdb the_oracles_whimsy
```

