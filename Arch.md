# 引导

计算机类型

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221015124514677.png" alt="image-20221015124514677" style="zoom: 50%;" />

注意没有实现MISD的计算机。

评价指标：

Response time响应时间：

CPU time：cpu的计算时间，不包括等待IO的时间，氛围用户模式和系统模式时间两种。

Throughput吞吐量：单位时间内完成工作的量

MIPS(Millions of Instructions per Second): 

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221015132216199.png" alt="image-20221015132216199" style="zoom:33%;" />

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221015132353438.png" alt="image-20221015132353438" style="zoom:50%;" />

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221015132438363.png" alt="image-20221015132438363" style="zoom:50%;" />



# 流水线Pipeline

思想：一个流程分成多个步骤，每个步骤有单独负责的硬件，每个时刻每个硬件都在工作，处理不同的指令。

流水线寄存器：RISC-V的五个步骤IF/ID -> ID/EX -> EX/MEM -> MEM/WB，每个阶段后都有一个寄存器，可以存运算的结果。

hazard冒险/竞争：指令无法在流水线正常线性执行的情况

structural hazard：硬件资源冲突

data hazard：读取数据冲突，通常是后面的指令需要的数据还没有被之前的指令计算完毕或存储完毕。

control hazard：涉及到条件跳转时，后面的指令在执行时还不知道是否应该跳转。

解决方法：

stall（阻塞）；暂停流水线，也称加入气泡bubble。已经在流水线的工作继续执行，但不再进行Instruction Fetch。 等到之前的指令已经基本完成，后面指令所需要的资源已经有了的时候，再继续流水线。stall过多会降低流水线性能，使得CPI比1大。

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221015174401144.png" alt="image-20221015174401144" style="zoom:33%;" />

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221015174456505.png" alt="image-20221015174456505" style="zoom:33%;" />



- 结构冒险

    - 前后指令同时访问相同的硬件资源：一般是同时访问reg file或同时读写内存。

    - 解决方法：

        直接stall（效率低）

        double bump：一个时钟内，前半个时钟沿写，后半个时钟沿读（先写后读，保证指令顺序逻辑），这样不会同时读或同时写寄存器或内存。此方法不需要检测hazard

- 数据冒险

    - 指的是指令在某一阶段所需要的数据并未被之前的指令处理完毕（如计算完但未写入reg file / memory）。导致流水线上出现数据逻辑与指令顺序不一致的情况。

    - R型：第一条指令为R型，需要最后一步WB才写回寄存器，但下面指令再第二阶段就已经需要访问该数据。注意由于double bump的存在，同一个时钟内不会导致数据冒险（先写后读，逻辑正确）。所以这种情况会影响未来两个时钟周期内的数据访问。

    - 解决方法：旁路（forwading，bypass）

        R型指令的计算结果（目标寄存器的值）在EX阶段就已经被计算出来，所以可以设置pipeline register存储计算出的值，在下一条指令需要的时候直接读即可，不需要读写回的数据。
		
    - load数据冒险：ld指令也是在最后才写回，但是其需要先计算地址值然后在MEM阶段才能真正读到寄存器的目标值，所以比R型晚了一步。一样可以设置MEM/WB的寄存器，但此时只能解决其指令隔一条指令的指令的数据冒险问题，对于load情况下的下一条指令，如果发生数据冒险，必须stall。
    
        <img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221016205354247.png" alt="image-20221016205354247" style="zoom: 50%;" />
    
    - 汇编优化：load stall比较影响性能，生成汇编代码时，可以通过改变指令顺序的方式，尽量避免出现load stall的情况。
    
    - 广义数据冒险
    
        以上提到的数据冒险都是RAW（Read After Write）型，也是唯一在普通五级流水线上会发生的类型。
    
        但如果流水线较为复杂，如涉及到多步的浮点运算，冒险未必都是RAW。也可能是WAR或者WAW。
    
        RAW：写后读，A是写，其后的指令B是读，但是由于流水线的存在，B的读实际发生在了A的写前面，导致B读到了旧值。可以用double bump及forwading的方法解决。是唯一可能在RISC-V普通五级流水线中出现的依赖类型。
    
        WAR：读后写，A是读，其后的指令B是写，但是由于流水线的存在，B的写实际发生在了A的读前面，导致A读到了新值（然而逻辑上A不应该读到还出现的值）。较为少见，一般在复杂流水线中出现，stall解决。
    
        WAW：写后写，A是写，其后的指令B也是写，但是由于流水线的存在，B的写实际发生在了A的写前面，导致A写的值又再次覆盖了B（逻辑上应该最终是B写的值）。WAW依赖存在的流水线，一定有存在多个阶段会写相同目标，否则不可能发生。也不常见，stall解决。

- 控制冒险

    - 由于存在跳转控制指令（beq，jal等），这些指令在执行完毕后会得到应该跳转的PC，改变指令执行顺序，但这是非流水线的逻辑，流水线中，下一条指令不知道跳转指令跳转的位置就需要马上读取下一条指令（PC顺序）。控制冒险较难解决。

    - stall：

        遇到跳转指令时（第一阶段IF就可以发现），马上stall下一条指令，直至跳转逻辑得到下一个PC（MEM阶段），然后执行下一条指令（如果发生了跳转，需要IF新PC）。需要阻塞3个时钟，效率很低。

        可以通过提早分析跳转条件来减少stall时钟：

        <img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221016210352109.png" alt="image-20221016210352109" style="zoom:33%;" />

        这样在在ID/EX处就可以知道是否跳转，从而只需要stall一个时钟。

    - delay slot（延迟槽）

        在跳转指令后执行一些与跳转逻辑无关的指令，需要编译器优化，较难。

        

        

        

















