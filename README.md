# CareNet
Building a Secure Software-defined Infrastructure for Home-based Healthcare


## CareNet API

### The API Abstraction

The API abstraction can be depicted as:

![alt text][abstraction]

[abstraction]: https://github.com/ACANETS/CareNet/blob/master/figs/API-v2.png "The CareNet API Abstraction"

+ **Abstraction 1 - Patients**:
For a human-centric infrastructure, the fundamental elements in the abstraction are the patients that can benefit from various services. To map the many-to-many relationship between patients and care-providers, our proposed abstraction provides the flexibility that each patient can access different services, and different patients (e.g. family members) can access the same service. Patients are the data sources in our model, and they have the ultimate authority to grant access policies to various data users.

+ **Abstraction 2 - Services**:
The ``Services'' presents an abstraction for the applications and the associated daemon processes that run across the CareNet framework. As the design of regulation compliant IoT applications requires data privacy preservation at endpoint, we employ Docker containerization technique to isolate IoT applications into a self-contained service. Each service consists of one or multiple running application(s), data exchange method among the applications, communication interfaces with the CareNet daemon, and the generated data stream. When initiated by the CareNet API, each service is given a unique service name and ID.

+ **Abstraction 3 - Groups**:
The API abstraction introduces the "Groups" concept that represents a collection of services with the associated patients and efficiently describes the service-service, patient-service inter-relationship properties. Services in the same group have to follow the same policy to be processed and to access the resources. The "Group" abstraction renders a clean and effective generalization to map the high-level requirements to the underlying constructs in XOS framework, and facilitates the scalable design of patient-side applications.


+ **Abstraction 4 - Resources**:
Since the CareNet infrastructure consists of both edge and core cloud computing resources, we can treat CareNet as a resource-rich platform where each service group can maintain a configuration of its accessible resources. To be specific, resources in the CareNet infrastructure include micro applications and services that build upon the underlying virtualized hardware. For example, we can opt for compute pools with various CPU models/numbers and memory sizes, block (Cinder) or object (Swift) data storage, and high/low bandwidth high/low latency network paths with various service chains, etc.

+ **Abstraction 5 - Users**:
"Users" abstracts the parties that are involved in the CareNet infrastructure management. Users can be ISP ITs, doctors, nurses, hospital ITs, patients, patient's relatives, etc., and they are in charge of designing various policies on demand, such as authentication policies, and resource assignment policies for services, etc.. It's worth noting that users have different levels of priority, which will determine the priorities of the policies written by users. For example, patients has the root priority of defining data access control by default and (s)he must assign the attribute authority.

+ **Abstraction 6 - Policy**:
"Policy" defines the CareNet infrastructure management behavior - it directs different service groups to access different resources by the policy defined configuration. Different users design their collection of policies for a service group to serve for their purposes. For example, patients has the right to specify the authorized user list and the attribute authority as required by HIPPA regulation. Application developers need to declare what services for the patient data require low network latency for effectiveness. ITs are responsible for defining the optimal network configuration for the overall system.
Policies offer a clean method to describe how service groups utilize resources in non-technical terms. It's important to notice that policies are designed to be reusable. Once a user creates a policy for a certain group, (s)he can reuse it repetitively for other groups. More efficiently, users other than the patients can share their policies to others in the CareNet community, since only patients' policies involve HIPPA compliance concern. The reusability highly reduces the labor to update and rewrite policies, thus improves portability, accuracy, and agility.

+ **Abstraction 7 - Policy Repository**:
The design of ``Policy Repository'' contributes another charm in our API abstraction. Repository allows different policies to co-exist and work together based on different roles of the policy writer. If still using the same example in policy for instance, three parities - patients, doctors, and ITs can design policies from different aspects to work for the same service group. This abstraction enables a highly collaborative management fashion so that all parties in CareNet infrastructure can participate to enforce regulatory compliance and improve system efficiency.

### The API Description

We propose a list of APIs to facilitate the management of CareNet with the aforementioned abstractions. The APIs fall into three categories: 1) service management - in CareNet framework, services created for the IoT endpoint devices must be containerized to maintain service isolation. A set of APIs are used to create and terminate containerized services. Moreover, clinic or hospital trusted third party application can be installed/removed with the agreement between patients and the care-providers; 2) performance related management - the network flows from different services have different perfor- mance requirements determined with the knowledge from both doctors and technical ITs. Some example requirements are: health-critical traffic (low latency requirement) versus regular bio-sensing traffic (no latency requirement); block storage for databases, file-exchange, etc. versus object storage for video processing and data backup, etc.; and high versus low perfor- mance computing resources etc.; and 3) policy enforcement: services in the same group have to follow the same policies in the policy repository. For example, to write the access control policy for Service A, patients need to first define the authorized user list, and assign different attribute authorities to different users. Then doctors will specify what kind of requirement they need for this service in order to guarantee the timeliness and effectiveness of the service results. ITs then need to look at the technical requirements of the service so that the service runs smoothly in the system. All the policies will finally merge together in the same repository to work collaboratively with different levels of priority.

The detailed API definition and description are depicted in the following table. 

|No.|API|Explaination|
|---|---|------------|
| 1 | bool createService (char* serviceTemplate, char* cmd, unsigned int hostPort, unsigned int containerPort, char* serviceName, Depen- dentService deplist) | Spawns a container to run a specific application from the CORD service template named serviceTemplate. DependentService is a data structure that list all existing dependent services for the current service. The function returns true if the container was successfully created, otherwise the function returns false. |
| 2 | bool removeService (char* serviceName, unsigned int delay) | Removes the service with the given service name. Waits up to delay seconds before sending SIGKILL signal to container. The function returns true if the container was successfully removed, otherwise the function returns false. |
| 3 | unsigned int createPatient (PatientInfo patient ServiceList serlist) | Creates a patient with the provided patient information and the patients associated services. PatientInfo is a JSON-formated key-value data structure. ServiceList is a list of service names. Returns the patients unique ID. |
| 4 | bool removePatient (unsigned int patID) | Removes a patient with the provided patient ID. Returns boolean value indication success or fail.|
| 5 | unsigned int createRes (ResourceList rlist) | Creates an accessible list of resources for certain service groups on the provided infrastructure. ResourceList is a JSON-formated key-value data structure. Returns the resource list ID. |
| 6 | bool removeRes (ResourceList rlist, unsigned int resID) | Removes a sub-list of resources rlist from the original resource list with ID=resID. Returns boolean indicator. |
| 7 | unsigned int createUser (UserInfo user) | Creates a user by using the provided user information. Returns a unique user ID. |
| 8 | bool removeUser (unsigned int uID) | Removes a user by using the provided user ID. Returns boolean indicator. |
| 9 | unsigned int createPolicy (Policy p) | Policy is a JSON-formatted data structure that contains multiple key-value fields. Some important key fields include resource, authentication list, attribute authority, etc. This function creates a new policy with the field of data indicated in Policy p. Returns the policy ID. |
| 10 | unsigned int createRepo (PolicyList polist) | Creates a new policy repository with a list of policy IDs. Returns the repository ID. |
| 11 | unsigned int createGroup (ServiceList serlist, unsigned int repoID) | Creates a service group with the provided service list and a policy repository ID. |
| 12 | void addServiceToGroup (char* serviceName, unsigned int gID) | Add an existing service to a service group with ID=gID. |
| 13 | void rmServiceFromGroup (char* serviceName, unsigned int gID) | Remove an existing service from a service group with ID=gID. |
| 14 | bool validateService (unsigned int gID, char* key) | Used for services that need to talk to public cloud. Validate the given service group ID to check if encrypted data has been modified. Returns true if data is safe.|