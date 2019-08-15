# Randomization

# Verilog Constraint Random Verification

Verilog has system function $random, which can be used to generate random input vectors. With this approach,we can generate values which we wouldnt have got, if listed manually. These constraints are at very low level of abstraction. 

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

     module Tb(); 
    integer num,seed,i,j; 
        initial 
        begin 
            for(j = 0;j<5 ;j=j+1) 
            begin 
            seed = 2; 
            $display(" seed is set %d",seed); 
            void'($urandom(seed)); 
            for(i = 0;i < 10; i=i+1) 
                begin 
                num = $urandom() % 10; 
                $write("| num=%2d |",num); 
                end 
              $display(" "); 
            end 
        end 
    endmodule
           
V C S   S i m u l a t i o n   R e p o r t 

seed is set           2
| num= 1 || num= 2 || num= 7 || num= 2 || num= 1 || num= 7 || num= 4 || num= 2 || num= 3 || num= 1 | 

 seed is set           2
| num= 1 || num= 2 || num= 7 || num= 2 || num= 1 || num= 7 || num= 4 || num= 2 || num= 3 || num= 1 | 

 seed is set           2
| num= 1 || num= 2 || num= 7 || num= 2 || num= 1 || num= 7 || num= 4 || num= 2 || num= 3 || num= 1 | 

 seed is set           2
| num= 1 || num= 2 || num= 7 || num= 2 || num= 1 || num= 7 || num= 4 || num= 2 || num= 3 || num= 1 | 

 seed is set           2
| num= 1 || num= 2 || num= 7 || num= 2 || num= 1 || num= 7 || num= 4 || num= 2 || num= 3 || num= 1 | 
           


# System Verilog Constraint Random Verification

Verification needs a better way to describe the constraints. SystemVerilog has randomization constructs to support todays verification needs. 

Example unsigned numbers

    module Rnd;
        integer unsigned address;
           initial 
             begin
                repeat(5)
                  begin
                     address = $urandom();
                     $display("address = %d",address);
                  end
            end 
    endmodule


V C S   S i m u l a t i o n   R e p o r t 

address =   98710838

address = 1474208060

address = 3196053373

address =  816460770

address =   41501707


The seed is an optional argument that determines the sequence of random numbers generated. The seed can be any integral expression. The random number generator (RNG) shall generate the same sequence of random numbers every time the same seed is used. 

Example using seed

    module Rnd;
     integer num,seed,i,j;
     initial 
        begin
            for(j=0;j<4;j=j+1)
            begin
             seed = 2;
             $display("seed is set %d",seed);
             void'($urandom(seed));
             for(i=0; i<10; i=i+1)
                begin
                  num=$urandom()%10;
                  $write("|num=%2d|",num);
                end
             $display(" ");
            end
        end
    endmodule

V C S   S i m u l a t i o n   R e p o r t 

seed is set           2
|num= 1||num= 2||num= 7||num= 2||num= 1||num= 7||num= 4||num= 2||num= 3||num= 1| 

seed is set           2
|num= 1||num= 2||num= 7||num= 2||num= 1||num= 7||num= 4||num= 2||num= 3||num= 1| 

seed is set           2
|num= 1||num= 2||num= 7||num= 2||num= 1||num= 7||num= 4||num= 2||num= 3||num= 1| 

seed is set           2
|num= 1||num= 2||num= 7||num= 2||num= 1||num= 7||num= 4||num= 2||num= 3||num= 1| 
           

# $urandom_range

The $urandom_range() function returns an unsigned integer within a specified range. 

The syntax for $urandom_range() is as follows: 
function int unsigned $urandom_range( int unsigned maxval,int unsigned minval = 0 ); 
The function shall return an unsigned integer in the range of maxval ... minval. 
If maxval is less than minval, the arguments are automatically reversed so that the first argument is larger than the second argument. 


Example random in range

    module rndm;
      integer num_1, num_2;
      initial
        begin
          repeat(20)
            begin
            #1;
               num_1 =$urandom_range(25,20);
               num_2 =$urandom_range(55,50);
               $display("num_1 = %0d, num_2 = %0d", num_1, num_2);
            end
        end
    endmodule
    
V C S   S i m u l a t i o n   R e p o r t 

num_1 = 22, num_2 = 52

num_1 = 21, num_2 = 50

num_1 = 21, num_2 = 55

num_1 = 24, num_2 = 53

num_1 = 20, num_2 = 55
           

If minval is omitted, the function shall return a value in the range of maxval ... 0

Example when minval is omitted

    module rndm;
      integer num_1, num_2;
      initial
        begin
          repeat(5)
          begin
           #1;
            num_1 =$urandom_range(3);
            num_2 =$urandom_range(5);
           $display("num_1 = %0d, num_2 = %0d", num_1, num_2);
          end
        end
    endmodule

V C S   S i m u l a t i o n   R e p o r t

num_1 = 2, num_2 = 2

num_1 = 1, num_2 = 0

num_1 = 3, num_2 = 5

num_1 = 0, num_2 = 3

num_1 = 2, num_2 = 5
           

The scope randomize function, randomize(), enables users to randomize data in the current scope.
Variables which are passed as arguments are randomized and there is no limit on the number of arguments. Variables which are in the constraint block and not passed as arguments to randomize() function are not randomized.
This gives better control over the $random,as it allows to add constraints using inline constraints and constraint solver gives valid solution. For simpler applications,randomize() function leads to stright forward implimentation.

        module scope_rndm;
          integer Var;
            initial
            begin
                for (int i=0; i<6;i++)
                if(randomize(Var))
                  $display("Randomization sucessfull:Var= %d0", Var);
                else
                  $display("Randomization failed");
                  $finish;
             end
        endmodule

R E S U L T

Randomization sucessfull:Var=   1330450070

Randomization sucessfull:Var= -19672867630

Randomization sucessfull:Var=  13487005760

Randomization sucessfull:Var=  -2062410490

Randomization sucessfull:Var=  14929494250

Randomization sucessfull:Var= -13791963250

Simulation complete 


In the following example Variable Var is randomized and MIN is not randomized. 

    module scope_rndm;
     integer Var;
     integer MIN;
       initial
         begin
          MIN=50;
          for (int i=0; i<5; i++)
          if(randomize(Var) with { Var <100; Var> MIN;})
           $display("Randomization sucessfull:Var= %d0 Min = %0d", Var, MIN);
           else
           $display("Randomization failed");
           $finish;
         end
     endmodule


R E S U L T

Randomization sucessfull:Var=          620 Min = 50

Randomization sucessfull:Var=          680 Min = 50

Randomization sucessfull:Var=          780 Min = 50

Randomization sucessfull:Var=          900 Min = 50

Randomization sucessfull:Var=          810 Min = 50

