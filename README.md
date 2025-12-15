# Dynamic-Instruction-Scheduling
Designed a simulator for an out-of-order superscalar processor that fetches and issues N instructions per cycle. 
The pipeline is a 9 stage pipeline that runs in the following manner:
do { 
Retire();     // Retire up to WIDTH consecutive 
             // “ready” instructions from the head of 
            // the ROB.
            
Writeback(); // Process the writeback bundle in WB: 
            // For each instruction in WB, mark the 
          // instruction as “ready” in its entry in 
          // the ROB.
          
Execute(); // From the execute_list, check for 
          // instructions that are finishing 
          // execution this cycle, and: 
          // 1) Remove the instruction from 
          //    the execute_list. 
          // 2) Add the instruction to WB. 
          // 3) Wakeup dependent instructions (set 
          // their source operand ready flags) in  
          // the IQ, DI (the dispatch bundle), and 
          // RR (the register-read bundle). 

Issue();  // Issue up to WIDTH oldest instructions 
          // from the IQ. 
          // To issue an instruction: 
          // 1) Remove the instruction from the IQ. 
          // 2) Add the instruction to the 
          //    execute_list. Set a timer for the 
          //    instruction in the execute_list that 
          //    will allow you to model its execution 
          //    latency. 

Dispatch(); // If DI contains a dispatch bundle: 
            // If the number of free IQ entries is less 
            // than the size of the dispatch bundle in 
            // DI, then do nothing. If the number of 
            // free IQ entries is greater than or equal 
            // to the size of the dispatch bundle in DI, 
            // then dispatch all instructions from DI to 
            // the IQ. 

RegRead();  // If RR contains a register-read bundle: 
            // If DI is not empty (cannot accept a 
            // new dispatch bundle), then do nothing. 
            // If DI is empty (can accept a new dispatch 
            // bundle), then process the 
            // register-read bundle and advance it from 
            // RR to DI. 

Rename();    // If RN contains a rename bundle: 
            // If either RR is not empty (cannot accept 
            // a new register-read bundle) or the ROB 
            // does not have enough free entries to 
            // accept the entire rename bundle, then do 
            // nothing. 
            // If RR is empty (can accept a new 
            // register-read bundle) and the ROB has 
            // enough free entries to accept the entire 
            // rename bundle, then process (see below) 
            // the rename bundle and advance it from 
            // RN to RR. 

Decode();   // If DE contains a decode bundle: 
            // If RN is not empty (cannot accept a new 
            // rename bundle), then do nothing. 
            // If RN is empty (can accept a new rename 
            // bundle), then advance the decode bundle 
            // from DE to RN. 
            // Do nothing if either (1) there are no 
            // more instructions in the trace file or 
            // (2) DE is not empty (cannot accept a new 
            // decode bundle).

Fetch();  // If there are more instructions in the 
          // trace file and if DE is empty (can accept 
          // a new decode bundle), then fetch up to 
          // WIDTH instructions from the trace file 
          // into DE. Fewer than WIDTH instructions 
          // will be fetched only if the trace file 
          // has fewer than WIDTH instructions left.

} while (Advance_Cycle()); 
// Advance_Cycle performs several functions.  
// First, it advances the simulator cycle. 
// Second, when it becomes known that the  
// pipeline is empty AND the trace is depleted, 
// the function returns “false” to terminate 
// the loop. 
