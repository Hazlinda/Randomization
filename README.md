# Randomization

Verilog Constraint Random Verification

Verilog has system function $random, which can be used to generate random input vectors. With this approach,we can generate values which we wouldnt have got, if listed manually. 

The below example shows the generation of numbers from 0 to N.

    module verilog_crv;
    integer add_1;
    reg [31:0] add_2;
    integer add_3;
  
    initial
      begin
        repeat(5)
          begin
          #1
          add_1 = $random %10; //it will randomize 0-9 (minus 1) (include -ve)
          add_2 = {$random %10}; //it will randomize 0-10 (include -ve)
          add_3 = $unsigned ($random) %10;//it will randomize 0-9 (+ve only)   
          end 
      end 

    initial 
    $monitor("add_3 = %0d;add_2 = %0d;add_1 = %0d",add_3,add_2,add_1); 

    endmodule
 
V C S   S i m u l a t i o n   R e p o r t
 
add_1 = x;  add_2 = x;          add_3 = x
add_1 = 8;  add_2 = 4294967287; add_3 = 7
add_1 = -9; add_2 = 7;          add_3 = 7
add_1 = -1; add_2 = 4294967292; add_3 = 1
add_1 = 9;  add_2 = 8;          add_3 = 7
add_1 = 9;  add_2 = 2;          add_3 = 9
          
       
Some specification require the range to start from non Zero number. MIN + {$random} % (MAX - MIN ) will generate random numbers between MIN and MAX. 

    module Tb(); 
    integer add; 

      initial 
        begin 
          repeat(5) 
          begin 
          #1; 
          add = 40 + {$random} % (50 - 40) ; //it will start compile randomize(50-40) then add 40
          $display("add = %0d",add); 
          end 
        end 
    endmodule 

V C S   S i m u l a t i o n   R e p o r t 

add = 48
add = 47
add = 47
add = 47
add = 47
 
 
This below example show how to generate a random number between two ranges. The number should be between MIN1 and MAX1 or MIN2 and MAX2. 
  
    module Tb();
    integer add;
  
    initial
      begin
      repeat(5)
        begin
          #1
          if ($random %2)
            add = 40 + {$random}%(50-40);
          else
            add = 90 + {$random}%(100-90);
          $display ("add = %0d", add);
        end
      end
    endmodule

V C S   S i m u l a t i o n   R e p o r t 

add = 97
add = 47
add = 47
add = 42
add = 49

           
