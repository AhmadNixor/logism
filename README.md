I made da CPU

# TOY-8 — how to test it
 
There are three ways to check that the TOY-8 works, going from quickest to most thorough:
 
1. Run one of the demo programs and watch it compute something.
2. Run the test vectors, which check the individual circuits against truth tables.
3. Run the Python test bank, which checks the whole thing on its own.
You need Logisim-evolution (the `.jar` is in this folder) for the first two, and Python 3 as well for the test bank.
 
## The files
 
- `toy8_complete.circ` is the full thing, all 52 circuits. Open this one. The main circuit is `MACHINE_WIRED`.
- `alu_milestone1.circ` is just Part I (the ALU) on its own.
- `programs/` has the 10 demo programs.
- `test_vectors/` has the truth-table files, one per circuit.
- `test_bank/toy8_testbank.py` is the automated tester.
- `full submission/images/` has a screenshot of every circuit.
## 1. Running a program
 
The files in `programs/` are little TOY-8 programs. Here's what each one should leave in register R when it finishes:
 
| Program | Expected R |
|---------|-----------|
| demo_halt | 0x00 |
| demo_loadaddr | 0x09 |
| demo_load | 0x2A |
| demo_add | 0x08 |
| demo_and | 0x0C |
| demo_xor | 0xC3 |
| demo_store | 0x07 |
| demo_branch_taken | 0x07 |
| demo_branch_skip | 0x09 |
| demo_sum | 0x09 |
 
The simplest way is in the Logisim window:
 
1. Open `toy8_complete.circ`.
2. Go into the `MACHINE_WIRED` circuit (double-click it on the left).
3. Right-click the memory block, pick Load Image, and choose a file from `programs/`.
4. Turn on the clock (Simulate menu, Ticks Enabled) and let it run until it stops.
5. Look at the R output. It should match the table.
If you'd rather not click through it, you can run it from the terminal instead:
 
```
java -jar logisim-evolution-4.0.0-all.jar -t table --toplevel-circuit MACHINE -l programs/demo_add.hex toy8_complete.circ
```
 
The last line it prints is the final state, and the 8-bit column is R. For demo_add that's `0000 1000`, which is 0x08.
 
## 2. Running the test vectors
 
A test vector file is just a list of inputs with the correct outputs next to them. Logisim runs every row through a circuit and tells you how many passed. There's one file per circuit, and the name matches, so `add8_tests.txt` goes with the `ADD8` circuit, `alu8_tests.txt` with `ALU8`, and so on.
 
From the terminal, the circuit name is the second argument:
 
```
java -jar logisim-evolution-4.0.0-all.jar -w ADD8 test_vectors/add8_tests.txt toy8_complete.circ
```
 
It prints something like `Passed: 45, Failed: 0`. You want Failed to be 0 every time. Just swap the circuit name and the file to test a different one, for example `-w ALU8 test_vectors/alu8_tests.txt`.
 
You can also do this in the GUI: open a circuit, then Simulate, Test Vector, and load the matching file.
 
## 3. Running the test bank
 
`test_bank/toy8_testbank.py` checks everything in one go. The point of it is that it works out the right answers with its own Python code first, then compares the circuit to that, so passing actually means the hardware matches the spec and not just itself. It does three things: runs all the test vectors, throws a few hundred generated cases at the ALU, and runs a bunch of random programs on both a Python version of the CPU and the real one to make sure they agree.
 
To run it:
 
```
cd test_bank
python3 toy8_testbank.py
```
 
It finds the `.circ` and `.jar` by itself. If you want it faster you can turn the numbers down:
 
```
python3 toy8_testbank.py --alu 20 --progs 10
```
 
When it's happy the last line says `OVERALL: ALL TESTS PASSED`. It starts a fresh Logisim for each check, so the full run takes a few minutes.
 
## What I got when I ran it
 
All of it came back with no failures: the 33 test vectors, the ALU cases, all 10 demo programs matching their expected R, and the random programs agreeing with the Python emulator. So running the steps above should give you the same, Failed: 0 across the board and ALL TESTS PASSED at the end.
