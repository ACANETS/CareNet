# CareNet
Building a Secure Software-defined Infrastructure for Home-based Healthcare


## CareNet API

### The API Abstraction

The API abstraction can be depicted as:

![alt text][abstraction]

[abstraction]: https://github.com/ACANETS/CareNet/blob/master/figs/API-v2.eps "The CareNet API Abstraction"

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