// testbench.sv
`timescale 1ns/1ps

module testbench;

  // Data types
  int int_var;
  logic logic_var;
  bit bit_var;
  byte byte_var;
  shortint shortint_var;
  longint longint_var;
  integer integer_var;
  time time_var;

  // Arrays
  int int_array[3:0];
  logic logic_array[2:0];
  bit bit_array[1:0];
  byte byte_array[0:7];
  shortint shortint_array[7:0];
  longint longint_array[6:0];
  integer integer_array[5:0];
  time time_array[4:0];

  // Packed array
  bit [7:0] packed_array[3:0];

  // Dynamic array
  int dynamic_array[];

  // Associative array
  int associative_array[string];

  // Fork-join examples
  task fork_join_example;
    fork
      begin
        #10 $display("Fork-join block 1");
      end
      begin
        #20 $display("Fork-join block 2");
      end
    join
  endtask

  task fork_join_any_example;
    fork
      begin
        #10 $display("Fork-join_any block 1");
      end
      begin
        #20 $display("Fork-join_any block 2");
      end
    join_any
  endtask

  task fork_join_none_example;
    fork
      begin
        #10 $display("Fork-join_none block 1");
      end
      begin
        #20 $display("Fork-join_none block 2");
      end
    join_none
  endtask

  initial begin
    // Declare and instantiate the Packet variable inside the initial block
    Packet pkt = new();

    // Test data types
    int_var = 10;
    logic_var = 1'b1;
    bit_var = 1'b0;
    byte_var = 8'hFF;
    shortint_var = -16'h8000;
    longint_var = 32'h7FFFFFFF;
    integer_var = 64'hFFFFFFFF;
    time_var = 100;

    // Test arrays
    int_array = '{1, 2, 3, 4};
    logic_array = '{1'b0, 1'b1, 1'b0};
    bit_array = '{1'b1, 1'b0};
    byte_array = '{8'hFF, 8'h00, 8'hFF, 8'h00, 8'hFF, 8'h00, 8'hFF, 8'h00};
    shortint_array = '{16'hFFFF, 16'h0000, 16'hFFFF, 16'h0000, 16'hFFFF, 16'h0000, 16'hFFFF, 16'h0000};
    longint_array = '{32'hFFFFFFFF, 32'h00000000, 32'hFFFFFFFF, 32'h00000000, 32'hFFFFFFFF, 32'h00000000, 32'hFFFFFFFF};
    time_array = '{100, 200, 300, 400, 500};  // Corrected array size

    // Test packed array
    packed_array = '{8'hFF, 8'h00, 8'hFF, 8'h00};

    // Test dynamic array
    dynamic_array = new[3];  // Initialize dynamic array with size of 3
    dynamic_array[0] = 10;
    dynamic_array[1] = 20;
    dynamic_array[2] = 30;

    // Test associative array
    associative_array["key1"] = 100;
    associative_array["key2"] = 200;

    // Test fork-join
    fork_join_example();
    fork_join_any_example();
    fork_join_none_example();

    // Test randomization
    if (pkt.randomize()) begin
      $display("Randomized Packet: addr=%h, data=%b, dyn_data=%p", pkt.addr, pkt.data, pkt.dyn_data);
    end else begin
      $display("Randomization failed");
    end

    #100 $finish;  // End the simulation
  end

endmodule






// design.sv
class Packet;
    rand bit [7:0] addr;
    rand logic [3:0] data;
    rand int unsigned dyn_data[];

    // Constraints
    constraint addr_range { addr >= 0; addr < 256; }
    constraint data_range { data > 0; }
    constraint dyn_data_size { dyn_data.size() inside {2, 3, 4}; }
endclass

