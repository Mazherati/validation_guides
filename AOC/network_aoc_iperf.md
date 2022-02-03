# Benchmark for Network Interface Card (NIC)

All commands are run in a Linux terminal

## Preparation
Make sure you have an ethernet switch capable of delivering a signal at the advertised speed of the NIC. 

You will also need a second system with either the same NIC you are testing, or a NIC with an equivalent speed to the advertised speed for the NIC you are testing.

The system under test (SUT) will run the iperf3 benchmark test as the client, while the other system will act as the host.

Boot both systems into the same version of either Red Hat or Ubuntu. Once booted into an OS, make sure the NIC is running at the correct width and speed with the following command:

    $lspci -s NIC_bus_id -vvv

Install the necessary packages on both systems with the following commands (for Ubuntu):

    $sudo apt update && sudo apt -y install iperf3 numactl net-tools lshw

## Setup

Once finished installing all the packages, connect the two systems back to back using an appropriate network cable between the NICs of each system. Take note of which port on each system's NIC that is being connected to.

On the SUT, run the command:

    ethtool ethx

where 'x' is the port number being connected to on the NIC. 

Verify  "Link detected: yes", and that the advertised speed is accurate.

If you get "Link detected: no", run the command:

    ifconfig ethx up
 Repeat these steps for the host system.

 Set an ip address for both SUT and host with the command:

    ifconfig ethx 10.x.x.x

You may choose the last three digits of the ip address, just make sure the first digits is 10 . Also, make sure that the static ip address you set is different for each system.

For example, the **host** system, with NIC port 1 connected, may be set as:

    ifconfig eth1 10.0.0.1

while the **SUT** also with NIC port 2 connected may be set as:

    ifconfig eth2 10.0.0.2

The static ip addresses shown in the previous two examples will be used going forward, 10.0.0.1 for  host, and 10.0.0.2 for SUT.

To verify that the systems are linked by having them ping each other. Run the following command on SUT: 

    ping 10.0.0.1

## Running the test

The test must be run for both TCP and UDP.

### TCP

Host command:

    iperf3 -s -B host_ip

SUT command:

    iperf3 SUT_ip -c host_ip -i 1 -b 1g -w M -P 25 | grep SUM

### UDP

Host command:

    iperf3 -s -B host_ip -p 5003

SUT command:

    iperf3 -B SUT_ip -c host_ip -i 1 -b 1g -u -w M -P 25 | grep SUM

The test results should return 90% or higher of the advertised NIC speed.

### Adjustments and troubleshooting
If there are connection issues:

- Make sure both NICs are running at advertised speed and width
- Check the network cable being used as well as the network switch and verify both are sending a signal at the necessary speed
- Upgrade the firmware of both NICs
- If you get the error message:

    "No route to host"
    
    disable the firewall for both the SUT and host

If test results are not as expected:

- Adjust the MTU on both host and SUT, ensuring the MTU is same on both, using the command:

        $ifconfig ethx mtu 4500 up

    You may need to try several different mtu parameter values. Try not to go above 9000. 
    
    Some cards will give you an error message if the MTU set is too high, even with a MTU set lower than 9000 sometimes. If this happens just lower the MTU parameter and try setting it again.




