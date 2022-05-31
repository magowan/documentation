# Trust Model for Confidential Containers



## Lifecycle 
The Cloud Native security Paper [^2] describes the lifecycle relating to workloads in four phases 
- Develop
- Distribute
- Deploy
- Runtime

When we consider confidential containers there are lifecycle phases required before arriving at 
the runtime environment in which the workload is truely confidential. Threats can exist during 
these lifecycle phases and trust must be considered in the context of the lifecycle of the 
environment supporting confidential containers.


![Lifecycle Phases](./images/LifecyclePhases.svg)

Taking the four phases above we can map this as :

#### Develop
 - Preparation and build of container images
 - Preparation and build of the trusted boot image for the TEE. 
 - Signatures and encryption keys for containers are prepared and available
 
````` Completing building the boot image triggers the next phase.`````

#### Distribute
The Distribute stage includes all preparations before the guest executes its first instructions, 
and it will run some general and TEE-specific operations.
- configuring the cluster with TEE
- making the boot image available
- taking initial measurements
- provision secrets.
- allocation of guest resource
- loading guest fw and configuration occurs 
- TEE-specific steps relating to preparing attestation/secrets etc

The involved components include the ones that directly associated with the
protections that TEEs provide (e.g, tdx seam-loader, tdx module, psp/sev firmware and so on),
Pre-OS (e.g, guest firmware, guest kernel, initrd and so on) and specific components (e.g,
kata-agent, attestation-agent and so on). Also includes preparing secrets for delivery to the TEE.

```The first instruction being executed within the trusted boot image happens in the next phase```

#### Deploy
  - pod sandbox preparation
  - "pre flight checks" include attestation measurment checks
  - secret provisioning linked to boot attestation
  
  ```Receiving the request to pull and deploy a container happens in the runtime phase```


#### Runtime
- post boot attestation and secret provisioning
- procedure of image pulling, unpacking and 
  launching.
- the workload in the container starts to  run in TEEs.


These execution phases are sufficient to describe the execution of any forms of confidential
container. 



### REFACTOR BELOW INTO LIFECYCLE STAGES ABOVE

### Goals



Around the three execution stages of confidential container, the scope of the threat model of
confidential container includes:

<* ADD diagram*>

- Pre-container stage
  - Measure the TEE where confidential containers run, and store the measurements to a secure location.

- Container launch stage
  - Measure and attest the TEE where confidential containers run to prove the deployment and initialization of pre-container are valid and expected;
  - Pull the encrypted and signed container image in TEE;
  - Provision the runtime configurations through a secure and attested control plane with the protections of confidentiality and integrity.

- Workload operation stage
  - Provide the protections of confidentiality and integrity for the storage and transport of data derived from TEE outside the TEE instance;
  - Keep the dynamic measurement of TEE where confidential containers run, and store the measurements to a secure location;
  - Continuously launch the runtime attestations for TEE where confidential containers run according to the policy to ensure the TEEs are not comprised or out-of-date;
  - Migration of confidential containers between TEE instances in a secure manner.
  

## Trust model



For the confidential containers, the TEE running the confidential container needs to provide a
reliable measurement across the life cycle of a confidential container. An attestation service 
fully verifies the validity of TEE measurements.
The purpose is to provide evidence for the application, container and code related personas that 
the that the workload in the confidential
container is running in a verified and genuine TEE. In summary: the trust model of confidential
container needs to address the trustworthiness of a TEE.

The trust model of confidential container closely focuses on the establishment of a chain of 
trust, through the above execution stages:

- SecDevOps : build the code and securely create secrets, encrypted containers and container 
  signatures.

- Pre-container stage: establish attestation measurements and extend the chain of trust, 
   to include creation and initialization of  a TEE in a secure manner, including using measured 
  boot and 
  / or secure boot during pre-container bootstrap.

- Container launch stage: extend the chain of trust to allow the container image 
  pulling, unpacking and launching to happen in a secure manner.

- Workload operation stage: extend the chain of trust to the workload and the End User data,    
 to ensure the  workload, derived data and incoming data are protected and verified in a secure manner.

There are many ways for actors in the ecosystem who rely on the security guarantees of a TEE to
establish trust in the TEE. This is the so-called trust policy. One trust policy is to obtain
assurance of security statements about products through assessment by third-party evaluation
laboratories. Other trust policies include basing assurances on security statements from specific
vendors; community or other audit of open source components in hardware, firmware, and/or software;
and assessment or certification by industry or standards bodies[1: 5.1 Goal]. Theoretically, the
trust policy can be fine-grained to the component level, so there are many combinations of the
trust policies that a specific solution may adopt basing on component level. In fact, different
forms of confidential container and specific solutions adopt the trust policy based on the
granularity of execution stage, that is, all components involved in each stage will follow the
same trust policy.



Therefore, the level of trust ultimately determines what kind of trust model to adopt. The trust
model needs to define their own trust policies for the execution stages. There are many approaches
to implement trust policies. The most important thing is to ensure the chain of trust is never
broken, and the validity of TEE in attestation procedure is proved sufficiently. 


