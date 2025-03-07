# TERMINOLOGY: Verilog Netlist
## The Verilog netlist represents the hardware description at the gate level, outlining how various gates and flipflops are coupled to construct the processor.

### A testbench in Verilog simulates and verifies design functionality.On other words, we attempt to replicate the hardware without the use of real flipflops or logic gates.

First, we'll download certain stuff needed for this:
To update packages from configured repositories, use the command "sudo apt-get update." In essence, it gets the system ready for package management operations like software updates and installations.


![Screenshot 2025-01-05 202602](https://github.com/user-attachments/assets/7ef4c08e-1329-4e01-b392-245b56bc255f)


#### Next we will use the command 

         sudo apt-get install iverilog gtkwave

to install iverilog(verilog synthesis and simulation tool) and gtkwave (a waveform viewer for viewing simulation output)

![Screenshot 2025-01-05 202706](https://github.com/user-attachments/assets/06a64930-edfd-46bc-8161-4c62e8ecc0f3)

### NEXT we will make a verilog file (end with .v )

IN my case the verilog code file name is ah.v , the verilog code is :
        
        module iiitb_rv32i(clk,RN,NPC,WB_OUT);
        input clk;
        input RN;
        //input EN;
        integer k;
        wire  EX_MEM_COND ;
        
        reg 
        BR_EN;
        
        //I_FETCH STAGE
        reg[31:0] 
        IF_ID_IR,
        IF_ID_NPC;                                
        
        //I_DECODE STAGE
        reg[31:0] 
        ID_EX_A,
        ID_EX_B,
        ID_EX_RD,
        ID_EX_IMMEDIATE,
        ID_EX_IR,ID_EX_NPC;      
        
        //EXECUTION STAGE
        reg[31:0] 
        EX_MEM_ALUOUT,
        EX_MEM_B,EX_MEM_IR;                        
        
        parameter 
        ADD=3'd0,
        SUB=3'd1,
        AND=3'd2,
        OR=3'd3,
        XOR=3'd4,
        SLT=3'd5,
        
        ADDI=3'd0,
        SUBI=3'd1,
        ANDI=3'd2,
        ORI=3'd3,
        XORI=3'd4,
        
        LW=3'd0,
        SW=3'd1,
        
        BEQ=3'd0,
        BNE=3'd1,
        
        SLL=3'd0,
        SRL=3'd1;
        
        
        parameter 
        AR_TYPE=7'd0,
        M_TYPE=7'd1,
        BR_TYPE=7'd2,
        SH_TYPE=7'd3;
        
        
        //MEMORY STAGE
        reg[31:0] 
        MEM_WB_IR,
        MEM_WB_ALUOUT,
        MEM_WB_LDM;                      
        
        
        output reg [31:0]WB_OUT,NPC;
        
        //REG FILE
        reg [31:0]REG[0:31];                                               
        //64*32 IMEM
        reg [31:0]MEM[0:31];                                             
        //64*32 DMEM
        reg [31:0]DM[0:31];   
        
        
        //assign EX_MEM_COND = (EX_MEM_IR[6:0]==BR_TYPE) ? 1'b1 : 1'b0;
                             //1'b1 ? (ID_EX_A!=ID_EX_RD) : 1'b0;
        
        always @(posedge clk or posedge RN) begin
            if(RN) begin
            NPC<= 32'd0;
            //EX_MEM_COND <=1'd0;
            BR_EN<= 1'd0; 
            REG[0] <= 32'h00000000;
            REG[1] <= 32'd1;
            REG[2] <= 32'd2;
            REG[3] <= 32'd3;
            REG[4] <= 32'd4;
            REG[5] <= 32'd5;
            REG[6] <= 32'd6;
            end
            //else if(EX_MEM_COND)
            //NPC <= EX_MEM_ALUOUT;
        
            //else if (EX_MEM_COND)begin
            //NPC = EX_MEM_COND ? EX_MEM_ALUOUT : NPC +32'd1;
            //NPC <= EX_MEM_ALUOUT;
            //EX_MEM_COND = BR_EN;
            //NPC = BR_EN ? EX_MEM_ALUOUT : NPC +32'd1;
            //BR_EN = 1'd0;
            //EX_MEM_COND <= 1'd0;
            //end
            else begin
            NPC <= BR_EN ? EX_MEM_ALUOUT : NPC +32'd1;
            BR_EN <= 1'd0;
            //NPC <= NPC +32'd1;
            //EX_MEM_COND <=1'd0;
            IF_ID_IR <=MEM[NPC];
            IF_ID_NPC <=NPC+32'd1;
            end
        end
        
        always @(posedge RN) begin
            //NPC<= 32'd0;
        MEM[0] <= 32'h02208300;         // add r6,r1,r2.(i1)
        MEM[1] <= 32'h02209380;         //sub r7,r1,r2.(i2)
        MEM[2] <= 32'h0230a400;         //and r8,r1,r3.(i3)
        MEM[3] <= 32'h02513480;         //or r9,r2,r5.(i4)
        MEM[4] <= 32'h0240c500;         //xor r10,r1,r4.(i5)
        MEM[5] <= 32'h02415580;         //slt r11,r2,r4.(i6)
        MEM[6] <= 32'h00520600;         //addi r12,r4,5.(i7)
        MEM[7] <= 32'h00209181;         //sw r3,r1,2.(i8)
        MEM[8] <= 32'h00208681;         //lw r13,r1,2.(i9)
        MEM[9] <= 32'h00f00002;         //beq r0,r0,15.(i10)
        MEM[25] <= 32'h00210700;         //add r14,r2,r2.(i11)
        //MEM[27] <= 32'h01409002;         //bne r0,r1,20.(i12)
        //MEM[49] <= 32'h00520601;         //addi r12,r4,5.(i13)
        //MEM[50] <= 32'h00208783;         //sll r15,r1,r2(2).(i14)
        //MEM[51] <= 32'h00271803;         //srl r16,r14,r2(2).(i15) */
        
        //for(k=0;k<=31;k++)
        //REG[k]<=k;
        /*REG[0] <= 32'h00000000;
        REG[1] <= 32'd1;
        REG[2] <= 32'd2;
        REG[3] <= 32'd3;
        REG[4] <= 32'd4;
        REG[5] <= 32'd5;
        REG[6] <= 32'd6;
        REG[7] = 32'd7;
        REG[6] = 32'd6;
        REG[7] = 32'd7;
        REG[8] = 32'd8;
        REG[9] = 32'd9;
        REG[10] = 32'd10;
        REG[11] = 32'd11;
        REG[12] = 32'd12;
        REG[13] = 32'd13;
        REG[14] = 32'd14;
        REG[15] = 32'd15;
        REG[16] = 32'd16;
        REG[17] = 32'd17;*/
        /*end
        else begin
            if(EX_MEM_COND==1 && EX_MEM_IR[6:0]==BR_TYPE) begin
            NPC=EX_MEM_ALUOUT;
            IF_ID=MEM[NPC];
            end
        
            else begin
            NPC<=NPC+32'd1;
            IF_ID<=MEM[NPC];
            IF_ID_NPC<=NPC+32'd1;
            end
        end*/
        end
        //I_FECT STAGE
        
        /*always @(posedge clk) begin
        
        //NPC <= rst ? 32'd0 : NPC+32'd1;
        
        if(EX_MEM_COND==1 && EX_MEM_IR[6:0]==BR_TYPE) begin
        NPC=EX_MEM_ALUOUT;
        IF_ID=MEM[NPC];
        end
        
        else begin
        NPC<=NPC+32'd1;
        IF_ID<=MEM[NPC];
        IF_ID_NPC<=NPC+32'd1;
        end
        end*/
        
        
        //FETCH STAGE END
        
        //I_DECODE STAGE 
        always @(posedge clk) begin
        
        ID_EX_A <= REG[IF_ID_IR[19:15]];
        ID_EX_B <= REG[IF_ID_IR[24:20]];
        ID_EX_RD <= REG[IF_ID_IR[11:7]];
        ID_EX_IR <= IF_ID_IR;
        ID_EX_IMMEDIATE <= {{20{IF_ID_IR[31]}},IF_ID_IR[31:20]};
        ID_EX_NPC<=IF_ID_NPC;
        end
        //DECODE STAGE END
        
        /*always@(posedge clk) begin
        if(ID_EX_IR[6:0]== BR_TYPE)
        EX_MEM_COND <= EN;
        else
        EX_MEM_COND <= !EN;
        end*/
        
        
        //EXECUTION STAGE
        
        always@(posedge clk) begin
        
        EX_MEM_IR <=  ID_EX_IR;
        //EX_MEM_COND <= (ID_EX_IR[6:0] == BR_TYPE) ? 1'd1 :1'd0;
        
        
        case(ID_EX_IR[6:0])
        
        AR_TYPE:begin
            if(ID_EX_IR[31:25]== 7'd1)begin
            case(ID_EX_IR[14:12])
        
            ADD:EX_MEM_ALUOUT <= ID_EX_A + ID_EX_B;
            SUB:EX_MEM_ALUOUT <= ID_EX_A - ID_EX_B;
            AND:EX_MEM_ALUOUT <= ID_EX_A & ID_EX_B;
            OR :EX_MEM_ALUOUT <= ID_EX_A | ID_EX_B;
            XOR:EX_MEM_ALUOUT <= ID_EX_A ^ ID_EX_B;
            SLT:EX_MEM_ALUOUT <= (ID_EX_A < ID_EX_B) ? 32'd1 : 32'd0;
        
            endcase
            end
            else begin
                case(ID_EX_IR[14:12])
                ADDI:EX_MEM_ALUOUT <= ID_EX_A + ID_EX_IMMEDIATE;
                SUBI:EX_MEM_ALUOUT <= ID_EX_A - ID_EX_IMMEDIATE;
                ANDI:EX_MEM_ALUOUT <= ID_EX_A & ID_EX_B;
                ORI:EX_MEM_ALUOUT  <= ID_EX_A | ID_EX_B;
                XORI:EX_MEM_ALUOUT <= ID_EX_A ^ ID_EX_B;
                endcase
            end
        
        end
        
        M_TYPE:begin
            case(ID_EX_IR[14:12])
            LW  :EX_MEM_ALUOUT <= ID_EX_A + ID_EX_IMMEDIATE;
            SW  :EX_MEM_ALUOUT <= ID_EX_IR[24:20] + ID_EX_IR[19:15];
            endcase
        end
        
        BR_TYPE:begin
            case(ID_EX_IR[14:12])
            BEQ:begin 
            EX_MEM_ALUOUT <= ID_EX_NPC+ID_EX_IMMEDIATE;
            BR_EN <= 1'd1 ? (ID_EX_IR[19:15] == ID_EX_IR[11:7]) : 1'd0;
            //BR_PC = EX_MEM_COND ? EX_MEM_ALUOUT : 1'd0; 
        end
        BNE:begin 
            EX_MEM_ALUOUT <= ID_EX_NPC+ID_EX_IMMEDIATE;
            BR_EN <= (ID_EX_IR[19:15] != ID_EX_IR[11:7]) ? 1'd1 : 1'd0;
        end
        endcase
        end
        
        SH_TYPE:begin
        case(ID_EX_IR[14:12])
        SLL:EX_MEM_ALUOUT <= ID_EX_A << ID_EX_B;
        SRL:EX_MEM_ALUOUT <= ID_EX_A >> ID_EX_B;
        endcase
        end
        
        endcase
        end
        
        
        //EXECUTION STAGE END
        		
        //MEMORY STAGE
        always@(posedge clk) begin
        
        MEM_WB_IR <= EX_MEM_IR;
        
        case(EX_MEM_IR[6:0])
        
        AR_TYPE:MEM_WB_ALUOUT <=  EX_MEM_ALUOUT;
        SH_TYPE:MEM_WB_ALUOUT <=  EX_MEM_ALUOUT;
        
        M_TYPE:begin
        case(EX_MEM_IR[14:12])
        LW:MEM_WB_LDM <= DM[EX_MEM_ALUOUT];
        SW:DM[EX_MEM_ALUOUT]<=REG[EX_MEM_IR[11:7]];
        endcase
        end
        
        endcase
        end
        
        // MEMORY STAGE END
        
        
        //WRITE BACK STAGE
        always@(posedge clk) begin
        
        case(MEM_WB_IR[6:0])
        
        AR_TYPE:begin 
        WB_OUT<=MEM_WB_ALUOUT;
        REG[MEM_WB_IR[11:7]]<=MEM_WB_ALUOUT;
        end
        
        SH_TYPE:begin
        WB_OUT<=MEM_WB_ALUOUT;
        REG[MEM_WB_IR[11:7]]<=MEM_WB_ALUOUT;
        end
        
        M_TYPE:begin
        case(MEM_WB_IR[14:12])
        LW:begin
        WB_OUT<=MEM_WB_LDM;
        REG[MEM_WB_IR[11:7]]<=MEM_WB_LDM;
        end
        endcase
        end
        
        
        
        endcase
        end
        //WRITE BACK STAGE END
        
        endmodule
        
    

and the test bench code is named as aha.v , which is :

        module iiitb_rv32i_tb;
        
        reg clk,RN;
        wire [31:0]WB_OUT,NPC;
        
        iiitb_rv32i rv32(clk,RN,NPC,WB_OUT);
        
        
        always #3 clk=!clk;
        
        initial begin 
        RN  = 1'b1;
        clk = 1'b1;
        
        $dumpfile ("iiitb_rv32i.vcd"); //by default vcd
        $dumpvars (0, iiitb_rv32i_tb);
          
          #5 RN = 1'b0;
          
          #300 $finish;
        
        end
        endmodule


### Now we will try to simulate this

For that we will use the command

                iverilog -o sim.vvp ah.v aha.v 

which is basically compiling the verilog files *ah.v* and *aha.v* and we are making an output file name sim.vvp , such that it could be executed with a verilog simulator

![Screenshot 2025-01-05 203219](https://github.com/user-attachments/assets/f28ea15b-8f46-462c-bec2-b83c84693568)


Then we will use the command

        vpp sim.vpp

which basically creates an dumpfile iiitb_rv32i.vcd opened for output

![Screenshot 2025-01-05 203402](https://github.com/user-attachments/assets/ff6d7f0d-1ad6-48fa-849a-fd91e0042f30)


### VIEWING WAVEFORM

To see the waveform we will use the command 

               gtkwave iiitb_rv32i.vcd 
                 
 which will open gtkwave to display the simulation result
 
(*Note : make sure u add the file necessery to check the waveform or you will get an empty waveform*)


![Screenshot 2025-01-05 203947](https://github.com/user-attachments/assets/3757f6cc-b59f-4a3e-8fa2-090ced28e1dc)

### Next we will try to analyize the simulated pipelines

In the middle panel one can see :

CLK (Clock Signal): This is the main clock signal for the entire system. It drives the timing and synchronization of all sequential logic.

NPC[31:0] (Next Program Counter): The next program counter value that points to the address of the next instruction to be executed.

RN (Register Number or Result Name): Likely represents a register number involved in the instruction, perhaps for identifying the destination register in the write-back stage.

WB_OUT[31:0] (Writeback Output): The data being written back to the register file from the execution unit or memory. It carries the final result of an instruction.

EX_MEM_ALUOUT[31:0] (ALU Output) : The result from the Arithmetic Logic Unit (ALU) during the execute stage, passed to the memory stage. 

EX_MEM_IR[31:0] (Instruction Register): Holds the current instruction being processed in the EX/MEM pipeline stage.
 
ID_EX_A[31:0] (Source Register A): One of the source operands fetched from the register file during the decode stage. 

ID_EX_B[31:0] (Source Register B): The second source operand for operations that require two operands, such as addition or subtraction.

ID_EX_IMMEDIATE[31:0] (Immediate Value)
For instructions that use immediate values (e.g., load immediate), this signal carries the immediate data.

ID_EX_IR[31:0] (Instruction Register): Holds the instruction in the decode/execution stage, similar to EX_MEM_IR, but earlier in the pipeline.

ID_EX_RD (Destination Register): Indicates the register number where the result of the current instruction will be written back.

IF_ID_IR[31:0] (Instruction Register): The instruction fetched from memory, waiting to be decoded.

IF_ID_NPC[31:0] (Next Program Counter in): The program counter associated with the instruction currently being fetched. 

MEM_WB_ALUOUT[31:0] (ALU Output): The ALU result passed to the write-back stage from the memory stage.

MEM_WB_IR[31:0] (Instruction Register in): The instruction currently in the memory/write-back stage. 

MEM_WB_LMD[31:0] (Load Memory Data): Carries data loaded from memory back to the processor, ready to be written into a register.

Clock Signal (CLK): Synchronizes the operation of all signals.

