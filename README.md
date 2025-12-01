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


