# Trust Model for Confidential Containers


## Trusted Execution Environment Boundary
Core to confidential containers is the use of a Trusted Execution Environment which protects 
memory inside the environment from access in the clear by any actor outside the environment. 
ANd core to the trust model is where within a cloud native 
stack to 
put the Trusted Execution 
boundary when looking to deliver confidential containers.

Three main boundaries for the Trusted Execution Boundary are:
- Node
- Pod
- Container/Container Processes

<*Add Diagram*>


Each boundary changes requirements on what components must exist inside the TEE, the 
form the image to run inside the TEE takes and ultimately on the trust model.

### Node boundary
This uses a virtual machine with hardware TEE capability to run a K8s node.
The kubelet is inside the TEE boundary and therefore communication between api-server and 
kubelet would cross the boundary.
The 

### Pod Boundary
This uses a micro virtual machine (microVM) with hardware TEE capability.
The microVM contains an agent 

### Container/Container Process boundary
This uses an enclave to run a LibOS (Intel SGX Enclave)


In all cases the application code interacts with data within a hardware TEE

A brief comparison:

| | node | pod  | container/process |
| | vm-cc | microvm-cc/kata-cc\*1 | enclave-cc |
| :-: | :-: | :-: | :-: |
| TCB footprint | Large | Medium | Small |
| Location of trust boundary\*2 | between K8s API server and kubelet | agent API\*3 | ECALL and OCALL |
| Threat Surface | High | Medium | Low |
| Deployment complexity | High | Low | Medium |
| Changes to workload Orchestration Experience | Low | Medium | High |

Note:
1. This doc may use kata-cc equivalent to microvm-cc in order to refer to a kata-specific component.
2. Here refers to the trust boundary of control plane.
3. A component used in kata and running in POD.


Efforts so far have focused on the pod boundary using Kata containers as the technology to run a 
pod within the TEE boundary. We expect this document to align the pod and container approaches 
over time, but we believe that the node approach cannot deliver the required trust for 
confidential containers without significant changes throughout the stack which are currently 
beyond the scope of our efforts.


## REFACTOR WHAT IS BELOW
In general, the trust model adopted by any confidential container based solutions can be binarized
into:
1) Partially trust the platform owner, operator and privileged components controlled by host
2) Completely distrust the platform owner, operator and privileged components controlled by host

where 1) means that some components intruded into Trusted Computing Base (TCB) can be 
unconditionally trusted by the tenant.
For example, a node based solution is likely to unconditionally trust the guest firmware provided
by host. On the contrary, a pod based solution is likely to verify the measurement of a
lightweight guest firmware provided by host during attestation procedure. Another example is, in
some cases, a tenant would like to trust the secret provisioned to the container through K8s API
server, and thus the threat vector "control plane attack" is not deemed as a trust boundary
violation. 2) is the preferred approach providing the protection for the confidential container
with the highest level of trust. Note: if a tenant fully trusts the platform owner, operator and
privileged components controlled by host, the Trusted Execution Environment (TEE) boundary will no 
longer be the trust boundary of
the confidential container, so in this case there is no need to use confidential computing.