# ansible-homework-2
second part with loopbacks and eigrp

[routers] (the router_id will be used in the loopback IP addresses)
router1 ansible_host=192.168.100.100 router_id=1
router2 ansible_host=192.168.100.101 router_id=2

[routers:vars]
ansible_connection=network_cli
ansible_network_os=cisco.ios.ios
ansible_user=cisco
ansible_password=cisco
ansible_ssh_common_args='-o StrictHostKeyChecking=no'

# EIGRP management / link network (use wildcard form, not /24) (I havn't learned EIGRP yet so I'm not sure what this is about)
mgmt_network_ip=192.168.1.0
mgmt_network_wildcard=0.0.0.255


---
- name: Configure Multiple Loopbacks and EIGRP (the actual EIGRP stuff I had to look up, I haven't learned about it yet and it didn't work for this lab. BUT the dynamic IP addressing worked)
  hosts: routers
  gather_facts: no

  tasks:

    - name: Configure Loopback0
      cisco.ios.ios_config:
        parents: "interface Loopback0"
        lines:
          - "ip address 10.0.0.{{ router_id }} 255.255.255.255" (uses the router_id variable in the inventory file) 

    - name: Configure Loopback1
      cisco.ios.ios_config:
        parents: "interface Loopback1"
        lines:
          - "ip address 10.1.1.{{ router_id }} 255.255.255.255"

    - name: Configure Loopback2
      cisco.ios.ios_config:
        parents: "interface Loopback2"
        lines:
          - "ip address 10.2.2.{{ router_id }} 255.255.255.255"

    - name: Configure EIGRP AS 100
      cisco.ios.ios_config:
        lines:
          - "router eigrp 100"
          - "network 10.0.0.0 0.0.0.255"
          - "network 10.1.1.0 0.0.0.255"
          - "network 10.2.2.0 0.0.0.255"
          - "network {{ mgmt_network_ip }} {{ mgmt_network_wildcard }}"

(EIGRP didn't work in this case so these commands don't do much)
    - name: Verify EIGRP neighbors
      cisco.ios.ios_command:
        commands:
          - show ip eigrp neighbors
      register: eigrp_neighbors

    - name: Display EIGRP neighbors
      debug:
        var: eigrp_neighbors.stdout_lines

    - name: Show loopback interfaces
      cisco.ios.ios_command:
        commands:
          - show ip interface brief | include Loopback
      register: loopbacks

    - name: Display loopbacks
      debug:
        var: loopbacks.stdout_lines
