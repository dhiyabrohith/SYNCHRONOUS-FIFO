# synchronous-FIFO
### [RTL]
```bash
module FIFO(clk,reset,w_en,wr_data,r_en,data_out,full,empty);
input clk,reset,w_en,r_en;
parameter WIDTH=8,
          DEPTH=16;
input [WIDTH-1:0]wr_data;
output full,empty;
output reg [WIDTH-1:0]data_out;
reg [WIDTH-1:0]mem[DEPTH-1:0];
parameter ADDR=4;
reg [ADDR-1:0]wr_ptr;
reg [ADDR-1:0]rd_ptr;
reg [DEPTH-1:0]count;
assign full=(count==DEPTH-1);
assign empty=(count==0);
always@(posedge clk or posedge reset)
begin
if(reset)
    begin
wr_ptr<=0;
rd_ptr<=0;
data_out<=0;
count<=0;
    end
else 
  begin 
if(w_en && !full)
       begin
		 mem[wr_ptr]<=wr_data;
		 wr_ptr<=wr_ptr+1;
		 count<=count+1;
		 end
if(r_en && !empty)
        begin
        data_out<=mem[rd_ptr];
        rd_ptr<=rd_ptr+1;
        count<=count-1;
        end
  end
end
endmodule		  
```
### [Test bench]
```bash
module FIFO_tb;
	// Inputs
	reg clk;
	reg reset;
	reg w_en;
	reg [7:0] wr_data;
	reg r_en;
	// Outputs
	wire [7:0] data_out;
	wire full;
	wire empty;
	// Instantiate the Unit Under Test (UUT)
	FIFO uut (
		.clk(clk), 
		.reset(reset), 
		.w_en(w_en), 
		.wr_data(wr_data), 
		.r_en(r_en), 
		.data_out(data_out), 
		.full(full), 
		.empty(empty)
	);
	always #5 clk=~clk;
	task write_fifo(input [7:0]q);
	begin
	@(posedge clk)
	 begin
    if(!full)
	 begin
	 w_en=1;
	 wr_data=q;
	 end
	 @(posedge clk)
	 w_en=0;
	 end
	 end
	 endtask
	 task read_fifo();
	begin
	@(posedge clk)
	 begin
    if(!empty)
	 begin
	 r_en=1;
	 end
	 @(posedge clk)
	 r_en=0;
	 end
	 end
	endtask
	initial begin
		// Initialize Inputs
		clk = 0;
		reset = 1;
		w_en = 0;
		wr_data = 0;
		r_en = 0;
		#100;
       reset = 0;
		 write_fifo(8'h1);
		 write_fifo(8'h2);
		 write_fifo(8'h3);
		 write_fifo(8'h4);
		 
		 write_fifo(8'h5);
		 write_fifo(8'h6);
		 write_fifo(8'h7);
		 write_fifo(8'h8);
		 
		 write_fifo(8'h9);
		 write_fifo(8'hA);
		 write_fifo(8'hB);
		 write_fifo(8'hC);

		 read_fifo();
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 #10;
	end
     initial begin
        $monitor("clk=%b ,reset=%b ,w_en=%b ,wr_data=%h ,r_en=%b ,data_out=%h ,full=%b ,empty=%b ",clk,reset,w_en,wr_data,r_en,data_out,full,empty);
      end
      initial begin
       #1000 $finish;
      end  
endmodule
```
