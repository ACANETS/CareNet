# Case Study

The CareNet framework and the APIs can address several research problems of strong clinical importance and urgency. These problems arise from real-world scenarios where the patient risks can be identified and the timely intervention and interaction between care providers and patients will facilitate speedy recovery. We have identified an essential real-world home-based stroke recovery use case that can leverage the proposed work. We present for the use case the significance of employing CareNet framework, and the simplicity and agility of regulation compliant management with CareNet APIs.

**Use Case: Kinect-based Real-time In-Home Stroke Rehabilitation**

While it is a general consensus that post-stroke rehabilitation can substantially help people achieve the best possible long-term outcome, the economic cost associated with in-patient post-stroke rehabilitation could be devastating for many stroke victims. To help the stroke survivor to reduce the cost without sacrificing healthcare quality, home rehabilitation has emerged as a valuable supplement to high cost outpatient facilities and/or nursing facilities. In addition to cost saving, home rehabilitation offers great flexibility, which allows the patients to tailor their rehabilitation program and to follow their own schedules.

One of the promising solutions is to develop a new in-home stroke rehabilitation system using new gaming consoles, such as the Microsoft Xbox Kinect. By capturing raw data from the Xbox Kinect sensors, we can track skeletal positions of the patient as they attempt physical rehabilitation exercises, evaluate how the patient is accomplishing the exercises, and score them accordingly. However, there are two major concerns for the care providers to render HIPAA/HITECH compliant interactive healthcare. (1) **Security**: because of the limited computation power of a clinic/hospital, the patient's video stream need to be transmitted over a public network and then to a computing platform (e.g. private hospital cloud or public cloud) for processing. Therefore, patient's data must be encrypted at network edge. In addition, before reaching the computing facility, care providers have to ensure that electronically transmitted patient data is not improperly modified as required by the HIPPA "integrity control". (2) **Performance**: a typical Kinect user scenario has a data generation rate at 300 Mbps (640x480 32-bit RGB images are generated at a 30 frames per second (fps)). Therefore, significant bandwidth is required to transfer such a large volume of video stream. In practice, patients also have to wear body sensors, such as a smart watch to collect the accelerometer data and life characteristics (e.g., heartbeat,  blood pressure). The data from body sensors are usually given low latency network priority over the Kinect video stream since bio-sensor data is often utilized to issue health-critical warning events, such as fall detection.

There are 3 major steps that leverage the proposed framework and APIs to express the use case workflow. The first step is the creation of services for different IoT sources. As suggested by regulatory compliance, “security at endpoint” is the key resort to build a secure IoT environment. Therefore, care providers need to create logically isolated services for each endpoint device. Besides, the service creation API permits the service dependency, which greatly facilitates the form of service chaining. The second step is to write different policies for different services, as the allocated resources for each service must meet the performance requirements by the care-providers. Thirdly, CareNet APIs offer the capability to validate the data integrity at any time of the data processing and issue early warnings for data discrepancy. 

We demonstrate the pseudo code as follows.

```c
/*
 * Psuedo Code Demo For Stroke Rehabilitation 
 */

// Step 1: Create the encryption, Kinect and watch service,
// add service dependency.
createService ("AES-Template", "initiate", hostPort1,
               containerPort1, "Encryption", NULL);
createService ("Kinect-Template", "initiate", hostPort2,
               containerPort2, "Kinect", "Encryption"); 
createService ("Watch-Template", "initiate", hostPort3,
               containerPort3, "Watch", "Encryption"); 

// Step 2: Allocate system resources to the services; create
// policy and policy repository;  create service group.                          
resKinect  = createRes (..., "max_delay_ms": "auto",
                             "bandwidth": 300);
resWatch   = createRes (..., "max_delay_ms": 3,
                             "bandwidth": "auto");
resEncrypt = createRes (..., "max_delay_ms": "auto",
                             "bandwidth": "auto");

policyKinect  = createPolicy ("resource": resKinect);
policyWatch   = createPolicy ("resource": resWatch);
policyEncrypt = createPolicy ("resource": resEncrypt);
repoRehab = createRepo ({policyKinect, policyWatch, 
                          policyEncrypt});

groupKinect = createGroup ({"Kinect", "Encryption"}, userlist);
groupWatch = createGroup ({"Watch", "Encryption"}, userlist);

// Step 3: Validate the integrity of data before
// reaching the computing facility.
if ( ! validateService (groupKinect, key) )
    issueWarning ("Kinect data discrepancy\n");
if ( ! validateService (groupWatch, key) )
    issueWarning ("Watch data discrepancy\n");              
```