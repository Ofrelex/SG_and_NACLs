# Security Groups and NACLs
Step-by-step process for **AWS Security Groups and Network ACLs (NACLs)**. This hands-on project will walk you through understanding, configuring, and testing how Security Groups and NACLs control traffic to/from AWS EC2 instances.
#
## Phase 1: Project Setup
1. Login to AWS Console
	* URL: [https://console.aws.amazon.com/](https://console.aws.amazon.com/)
* Use your IAM or root credentials.
2. Launch a Test EC2 Instance
	* Go to EC2 → Instances → Launch Instance
* Select Amazon Linux 2 or Ubuntu AMI.
* Choose t2.micro (Free Tier).
* In the Network Settings:
* Select default VPC or create a custom one.
* Select or create a Security Group (you’ll configure it later).
* Add a key pair for SSH access.
* Launch the instance.
#
## Phase 2: Understanding and Configuring Security Groups
### A. Basics of Security Groups
* Security Groups are stateful firewalls attached to EC2 instances.
* Rules are applied to allow inbound/outbound traffic based on protocols, ports, and sources/destinations.
### B. Scenario Implementations
1. Scenario 1: Allow HTTP (80) and SSH (22) inbound, all outbound
Go to EC2 → Security Groups → Edit inbound rules
	* Add rules:
	* Type: HTTP | Port: 80 | Source: 0.0.0.0/0
* Type: SSH | Port: 22 | Source: your IP or 0.0.0.0/0 (for testing)
* Edit **outbound rules**:
* Allow: All traffic | Destination: 0.0.0.0/0
2. Scenario 2: Allow inbound HTTP, remove outbound rules
   * In outbound rules, remove all entries (deny all traffic by default).
   * Result: HTTP requests can be received, but EC2 cannot initiate outbound requests.
3. Scenario 3: Remove all inbound and outbound rules
* Clear all inbound and outbound rules.
* Result: All traffic is denied; the instance is completely isolated.
4. Scenario 4: No inbound rules, allow all outbound
* Remove all inbound rules (default deny).
* Set outbound: Allow All Traffic to 0.0.0.0/0.
* Result: EC2 cannot be accessed from outside but can reach the internet.
#
## Phase 3: Understanding and Configuring Network ACLs (NACLs)
### A. Basics of NACLs
* NACLs are stateless firewalls at the subnet level.
* They evaluate both inbound and outbound traffic.
* Rules are evaluated in order, from lowest to highest rule number.
### B. Examine Default NACL Settings
1. Navigate to VPC → Network ACLs
2. Select the default NACL attached to the subnet your instance is in.
3. View Inbound and Outbound rules (typically allow all by default).
#
## Phase 4: NACL Rule Configurations
### Scenario 1: Modify inbound rules to allow all IPv4 traffic on all ports
* Edit Inbound Rules:
* Rule #: 100 | Type: All Traffic | Protocol: ALL | Port Range: ALL | Source: 0.0.0.0/0 | Allow
### Scenario 2: Modify outbound rules to allow all IPv4 traffic
* Edit Outbound Rules:
* Rule #: 100 | Type: All Traffic | Protocol: ALL | Port Range: ALL | Destination: 0.0.0.0/0 | Allow
### 🔸 Scenario 3: Deny all inbound traffic
* Edit Inbound Rules:
* Rule #: 100 | Type: All Traffic | Source: 0.0.0.0/0 | DENY
**Note**: Because NACLs are stateless, both directions (inbound and outbound) must be allowed for a successful communication.
#
## Phase 5: Observation and Testing
Use tools like:
* SSH Client or EC2 Connect to test SSH access.
* Use a web server like Apache or Nginx to serve a test page on HTTP port 80.
* Try using `curl`, `ping`, or web browser to simulate traffic.
* Modify the NACL and Security Group rules and observe the impact immediately.
#
##Summary of Key Concepts
| Component      | Type     | Scope        | Stateful | Rule Direction   | Default Behavior                     |
| -------------- | -------- | ------------ | -------- | ---------------- | ------------------------------------ |
| Security Group | Firewall | EC2 Instance | Yes      | Inbound/Outbound | All outbound allowed, inbound denied |
| NACL           | Firewall | Subnet       | No       | Inbound/Outbound | All traffic allowed in default NACL  |

