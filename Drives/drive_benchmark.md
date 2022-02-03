# Drive Benchmark
All commands run in Linux terminal

## Setup

Install the following packages:
- libaio
- libaio-devel
- fio
- vim

After installing all necessary packages, you will need to make the FIO test file. You can do so with the following command:

    touch fio_test.fio

Open the fio test file in vim with the following command:

    vim fio_test.fio

To begin editing in vim, press "i" for insert.
Once you are finished editing, enter "!wq" to write to the file and then  quit. Equivalent to save and quit.

To run the FIO test after editing the test file, with a log file to capture the results, use the following command:

    fio fio_test.fio > fio_results.log

## Fio file layout example

[global]\
ioengine=libaio\
direct=1\
buffered=0\
bs=1m\
iodepth=128\
numjobs=2\
time_based\
runtime=60\
rw=read

[nvme0]\
stonewall\
filename=/dev/nvme0

[sda]\
stonewall\
filename=/dev/sda

## Fio file parameters

**[global]** - This is the global default setting for jobs in the file

**direct=1** - If the value is true, use non-buffered I/O

**buffered=0** - The opposite of the direct parameter.If value is true, use buffered I/O

**ioengine=libaio** - Linux native asynchronous I/O. Linux only supports queued behavior with non-buffered I/O, hence direct=1 and buffered =0

**bs=1m** - Block size in bytes for I/O units. Test block sizes from 512b to 8M, where each step should be twice the block size of the previous step.

**iodepth=128** - The number of I/O the FIO test submits to the OS. Adjust this number as needed if performance numbers are not up to par. Default iodepth value is 1.

**numjobs=2** - Number of processes and threads performing the same workloud. Adjust this number as needed if performance numbers are not up to par. Default  numjobs value is 1.

**ramp_time=20** - The amount of time the specified workload will run for before  logging performance results. Use when performance needs to be settled before logging results.

**time_based** - Tells the FIO test to use a runtime parameter to end the test, instead of going by byte size.

**runtime=60** - Terminate job after specified amount of time.

**rw=read** - Parameter options for rw as follows:
- read - sequential read
- write - sequential write
- rw or readwrite - mixed sequential reads and writes
- randread - random read
- randwrite - random write
- randrw - mixed random reads and writes

**stonewall** - Wait for preceding jobs in the file to finish before starting next job, instead of trying to run simultaneously. 

**filename=/dev/sda** - Set the drive to run the current job on. To see a list of drives run the following command:

    lsblk