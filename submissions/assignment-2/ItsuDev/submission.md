---
week: 10
graded: true
counts_toward: Class Standing, Seatwork/Assignment/Recitation (10%)
estimated_time: 45 to 60 minutes
mode: Individual, take-home. Sign in with your Lab group IAM user. Submit a Pull Request to your section repository, then submit the Pull Request link in the Google Form.
coverage: VPC, CIDR, subnets, Availability Zones, route tables, internet and NAT gateways, security groups, network ACLs
due: 2026-10-07, 11:59 PM (Asia/Manila). The Pull Request and the Google Form must both be in by this time.
---

# Assignment 2: Explore a VPC

## About me

- GitHub username: ItsuDev
- Section: <IV-CCSAD>
- IAM user name that I signed in with: <ItsuDev>
- X: <110>

---

## Part A. Explore

### A1. The VPC

Default VPC IPv4 CIDR:

172.31.0.0/16

Number of addresses in that CIDR:

65,536

### A2. The subnets

| Availability Zone | IPv4 CIDR |
| --- | --- |
| ap-southeast-1a | 172.31.0.0/20 |
| ap-southeast-1b | 172.31.16.0/20 |
| ap-southeast-1c | 172.31.32.0/20 |

Screenshot 1. Save it as `screenshot-1-subnets.png` in your folder. The image line below shows it.

![Screenshot 1: subnet list](screenshot-1-subnets.png)

### A3. Available addresses

Available IPv4 addresses in each subnet:

4,091

Why is the number lower than 4,096?

AWS automatically reserves 5 IP addresses in every subnet for network administration, VPC router, DNS services, future expansion, and network broadcast address.

What uses the missing address in the subnet with the lowest number?

An Elastic Network Interface (ENI) attached to an active AWS resource running inside that subnet, such as an EC2 instance, NAT Gateway, or Load Balancer.

### A4. The route table

| Destination | Target |
| --- | --- |
| 172.31.0.0/16 | local |
| 0.0.0.0/0 | igw-... |

Screenshot 2. Save it as `screenshot-2-routes.png` in your folder. The image line below shows it.

![Screenshot 2: routes of the route table](screenshot-2-routes.png)

### A5. Public or private

Are the default subnets public or private? Which route proves it?

Public. The default route `0.0.0.0/0` targeting an Internet Gateway (`igw-...`) proves that traffic can be routed directly to and from the public internet.

### A6. The internet gateway

State of the internet gateway:

Attached

What happens to the default subnets if the gateway is detached?

The default subnets lose public internet connectivity. Instances within these subnets become unreachable from outside networks and cannot initiate outbound traffic to the internet.

### A7. NAT gateways

Number of NAT gateways:

0

Can a server in a new private subnet download updates? Why?

No. A private subnet lacks a direct route to an Internet Gateway, and without a NAT Gateway to translate private IPs for outbound requests, the server cannot establish internet connectivity to download updates.

### A8. The network ACL

| Rule number | Source | Allow or Deny |
| --- | --- | --- |
| 100 | 0.0.0.0/0 | Allow |
| * | 0.0.0.0/0 | Deny |

How is a network ACL different from a security group?

A Network ACL operates at the subnet boundary as a stateless firewall using ordered allow and deny rules. A Security Group operates at the instance/ENI level as a stateful firewall that only supports allow rules and automatically permits returning response traffic.

Screenshot 3. Save it as `screenshot-3-network-acl.png` in your folder. The image line below shows it.

![Screenshot 3: inbound rules of the network ACL](screenshot-3-network-acl.png)

### A9. The default security group

Inbound rule (type and source):

Type: All traffic | Source: sg-... (default security group ID)

Which resources can send traffic to an instance that uses it?

Only other instances and resources within the same VPC that are assigned to this exact default security group.

---

## Part B. Prepare

### B1. Plan two subnets

- Public subnet CIDR: 10.<1XX>.0.0/24
- Private subnet CIDR: 10.<1XX>.1.0/24

### B2. Route tables

Route table of the public subnet:

| Destination | Target |
| --- | --- |
| 10.<1XX>.0.0/16 | local |
| 0.0.0.0/0 | internet gateway |

Route table of the private subnet:

| Destination | Target |
| --- | --- |
| 10.<1XX>.0.0/16 | local |
| 0.0.0.0/0 | NAT gateway |

### B3. My VPC diagram

Tool used (Excalidraw, draw.io, Lucidchart, or paper):

Excalidraw

Save your diagram as `vpc-diagram.png` in your folder. The image line below shows it.

![B3: my VPC diagram](vpc-diagram.png)

### B4. Predict a change

Can you still open the web page from your laptop? Why?

No. Removing the `0.0.0.0/0` route breaks internet traffic routing to and from the Internet Gateway, rendering the instance unreachable via its public IP address.

Can the instance still reach another instance in the VPC? Why?

Yes. Local VPC communication relies on the `172.31.0.0/16 -> local` route, which remains active and allows internal instance-to-instance connectivity.

### B5. Place a database

Which subnet gets the database? Why?

The database should be placed in the private subnet (e.g., 10.X.1.0/24) because its route table does not have a route to the Internet Gateway (0.0.0.0/0 -> igw). This keeps the database isolated from direct exposure to the public internet, protecting sensitive data from external attacks while still allowing backend application servers in the public subnet to connect to it via local VPC routing.