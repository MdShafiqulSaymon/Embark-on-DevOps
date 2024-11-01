# Define variables for namespaces, interfaces, and logs
NS1 := ns1
NS2 := ns2
VETH1 := veth1
VETH2 := veth2
LOG_FILE := logs/tcpdump.log

# Create logs directory if it doesn't exist
init:
	mkdir -p logs

# Target for setting up network namespaces
setup:
	ip netns add $(NS1)
	ip netns add $(NS2)
	ip link add $(VETH1) type veth peer name $(VETH2)
	ip link set $(VETH1) netns $(NS1)
	ip link set $(VETH2) netns $(NS2)
	ip netns exec $(NS1) ip addr add 10.0.0.1/24 dev $(VETH1)
	ip netns exec $(NS2) ip addr add 10.0.0.2/24 dev $(VETH2)
	ip netns exec $(NS1) ip link set $(VETH1) up
	ip netns exec $(NS2) ip link set $(VETH2) up
	ip netns exec $(NS1) ip link set lo up
	ip netns exec $(NS2) ip link set lo up

# Target for testing connectivity with ping
test:
	ip netns exec $(NS1) ping -c 3 10.0.0.2

# Target for capturing packets with tcpdump
capture:
	ip netns exec $(NS1) tcpdump -i $(VETH1) -w $(LOG_FILE) &

# Target for cleaning up the environment
clean:
	ip netns del $(NS1) || true
	ip netns del $(NS2) || true
	rm -rf logs/*
