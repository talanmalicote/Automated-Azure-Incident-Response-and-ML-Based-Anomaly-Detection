# Automated Azure Incident Response & ML-Based Anomaly Detection

## Short Intro

This project implements a cloud security monitoring and automated incident response pipeline designed to detect anomalous Azure Blob Storage activity and respond to simulated data-exfiltration attempts. It combines Azure monitoring, KQL, Python, behavioral anomaly detection, Microsoft Entra ID, Azure Logic Apps, cloud forensics, and LLM-assisted incident reporting into a closed-loop security workflow.

The primary objective is to investigate whether behavioral anomaly detection can identify suspicious storage activity beyond what can be detected using static threshold-based rules, while reducing the amount of manual effort required during incident response.

## Technologies
- Micosoft Azure
- Python
- KQL
- Isolation Forest
- Scikit-learn
- Pandas
- SOAR
- LLM

## Features 
- Azure Blob Storage activity monitoring
- Behavioral baselines for users and service principals
- Detection of unusual download volume and request frequency
- Detection of access during unusual hours
- Detection of geographic deviations
- Detection of large numbers of unique objects accessed
- Isolation Forest-based anomaly detection
- Comparison with static KQL threshold-based detection
- Simulated data-exfiltration scenarios
- Automated Logic Apps response workflows
- Microsoft Entra ID service principal containment
- Storage access credential revocation
- Cloud evidence collection
- Incident timeline reconstruction
- LLM-assisted incident report generation
- Human validation of generated reports

## Progress Used
# 1. Define the Detection Problem

The project began by defining suspicious Azure Blob Storage behavior that could indicate data exfiltration. The focus was placed on behavioral changes such as unusually high download volume, rapid access patterns, unusual access times, geographic deviations, and access to large numbers of objects.

# 2. Create Normal Activity

Normal storage activity was simulated across multiple users and service principals. The simulated identities were given different access patterns to represent realistic variations in frequency, download volume, time of day, geographic location, and data transfer.

# 3. Create Attack Scenarios

Controlled attack scenarios were generated to represent potential data-exfiltration behavior. These scenarios included high-volume downloads, rapid download bursts, unusual access times, geographic deviations, and combined behavioral anomalies.

No real sensitive data or unauthorized systems were used.

# 4. Collect and Query Telemetry

Azure monitoring and Log Analytics were used to collect storage activity. KQL queries were created to identify download activity, establish behavioral baselines, and extract evidence for incident investigation.

# 5. Engineer Behavioral Features

Raw storage access events were transformed into features that could be analyzed by the machine learning model. These features included:

- Number of download requests within a time window
- Total bytes downloaded
- Number of unique blobs accessed
- Access during unusual hours
- Geographic deviation from normal activity
- Request frequency

# 6. Train the Anomaly Detection Model

An Isolation Forest model was trained primarily on normal activity. This approach was selected because the project did not initially depend on a large labeled dataset containing confirmed malicious Azure storage activity.

The model learned patterns associated with normal behavior and identified observations that differed significantly from those patterns.

# 7. Evaluate Detection Performance

The model was evaluated against simulated attack scenarios that were not used to establish the normal behavioral baseline.

Machine learning detection was compared with static KQL threshold-based detection using metrics such as:

- True positives
- False positives
- False negatives
- Precision
- Recall
- Detection rate

# 8. Automate Incident Response

Azure Logic Apps was used to orchestrate the response process after suspicious activity was detected. The workflow identified the affected identity, validated the alert, disabled the simulated compromised service principal, revoked relevant storage access, and recorded the response actions.

# 9. Collect Evidence

After detection and containment, Azure Storage API activity was analyzed to reconstruct the incident. The investigation focused on the timeline, source IP addresses, geographic information, identities involved, storage resources accessed, data transferred, detection time, containment time, and response actions.

# 10. Generate and Validate the Incident Report

Structured incident data was provided to an LLM to generate a preliminary incident report. The report included an executive summary, incident timeline, affected identity, scope of activity, detection details, containment actions, and recommended follow-up actions.

The generated report was reviewed against the underlying evidence to identify unsupported or inaccurate claims.

## What I learned 
- Behavioral baselines can provide more context than universal thresholds.
- Anomaly detection is useful for identifying unusual activity but does not independently prove malicious intent.
- Feature engineering has a significant impact on the quality of anomaly detection.
- Static KQL rules remain valuable for detecting clearly defined and high-confidence behaviors.
- Machine learning and rule-based detection can complement each other.
- Cloud incident response requires coordination between monitoring, identity management, storage access, automation, and evidence collection.
- Automated containment must be carefully controlled because incorrect responses can disrupt legitimate activity.
- Service principal security is an important part of protecting cloud storage resources.
- Incident timelines are easier to reconstruct when relevant telemetry is collected consistently.
- LLMs can assist with incident documentation, but generated reports must be validated against source evidence.
- Simulated attack scenarios are useful for testing detection logic when real-world labeled data is unavailable.

## What Could Be Improved 
- Use a larger and more diverse behavioral dataset.
- Include additional Azure telemetry sources.
- Create separate baselines for individual users and service principals.
- Test additional anomaly detection algorithms.
- Compare Isolation Forest with supervised models when labeled data becomes available.
- Improve geographic and identity-based risk scoring.
- Add more realistic variations in normal user behavior.
- Integrate the workflow with Microsoft Sentinel.
- Add analyst approval steps before high-impact containment actions.
- Expand automated response actions.
- Map detected behaviors to MITRE ATT&CK techniques.
- Improve detection of low-and-slow data exfiltration.
- Evaluate detection latency and containment time more rigorously.
- Test the LLM reporting component for factual accuracy, consistency, and unsupported claims.
- Add stronger logging and audit trails for all automated response actions.

## How to Run the Project
# Prerequisites
- Python 3.10 or later
- An Azure subscription
- An Azure Storage account with Blob Storage enabled
- An Azure Log Analytics workspace
- Azure Monitor diagnostic settings configured for storage activity
- Azure Logic Apps
- Microsoft Entra ID permissions for the controlled test environment
- Appropriate Azure CLI or Azure PowerShell access

# Clone the Repository
git clone https://github.com/your-username/azure-soar-anomaly-detection.git
cd azure-soar-anomaly-detection

# Create and Activate a Virtual Environment

# Windows
python -m venv .venv
.venv\Scripts\activate

# macOS / Linux
python3 -m venv .venv
source .venv/bin/activate

# Install Dependencies
pip install -r requirements.txt

# Generate Normal Activity
python simulation/generate_normal_activity.py

# Generate Attack Scenarios
python simulation/generate_attack_scenarios.py

# Engineer Features
python detection/feature_engineering.py

# Train the Model
python detection/train_model.py

# Run Anomaly Detection
python detection/detect_anomaly.py

# Evaluate the Model
python detection/evaluate_model.py

# Configure Azure Components

Before running the cloud-based response workflow:

1. Create or configure the Azure Storage account.
2. Create an Azure Log Analytics workspace.
3. Enable the required Azure Storage diagnostic settings.
4. Add and test the KQL queries in the kql/ directory.
5. Configure the Azure Logic Apps workflow.
6. Connect the workflow to the appropriate Azure and Microsoft Entra ID actions.
7. Use only test identities, test storage resources, and simulated data.
8. Verify that automated containment actions cannot affect production resources.

# Collect Evidence and Generate a Report
python forensics/collect_evidence.py
python forensics/generate_report.py

The project should be run in a controlled Azure environment. Do not use production identities, production storage accounts, or real sensitive data for testing.
  
## Repository Structure
azure-soar-anomaly-detection/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── architecture/
│   └── architecture.png
│
├── data/
│   ├── normal_activity.csv
│   ├── attack_scenarios.csv
│   └── test_dataset.csv
│
├── kql/
│   ├── blob_downloads.kql
│   ├── baseline_activity.kql
│   └── incident_evidence.kql
│
├── detection/
│   ├── feature_engineering.py
│   ├── train_model.py
│   ├── detect_anomaly.py
│   └── evaluate_model.py
│
├── simulation/
│   ├── generate_normal_activity.py
│   └── generate_attack_scenarios.py
│
├── soar/
│   ├── logic_app_workflow.json
│   └── response_actions.md
│
├── forensics/
│   ├── collect_evidence.py
│   ├── generate_report.py
│   └── incident_report.md
│
├── evaluation/
│   ├── results.csv
│   └── evaluation.md
│
└── docs/
    ├── methodology.md
    ├── threat_model.md
    └── limitations.md

## Limitations 
This project is a controlled security simulation and does not represent production Azure infrastructure.

Known limitations include:

- Simulated rather than real-world attack data
- Limited training dataset
- Potential false positives from behavioral changes
- IP-based geographic information may not represent the physical location of an attacker
- Anomaly detection identifies unusual behavior but does not independently establish malicious intent
- Automated containment can create operational risk if detection confidence is insufficient
- LLM-generated reports require human validation before being treated as authoritative incident documentation

## Future Improvements
Potential future improvements include:

- Larger behavioral datasets
- Additional Azure telemetry sources
- User-specific and service-principal-specific baselines
- Additional anomaly detection algorithms
- Supervised classification using labeled security datasets
- Integration with Microsoft Sentinel
- Additional automated containment actions
- Analyst approval workflows for high-impact responses
- Improved geographic and identity-based risk scoring
- Automated MITRE ATT&CK technique mapping
- More advanced detection of low-and-slow data exfiltration
- Improved LLM report validation
- Automated security metrics and dashboarding

## Disclamer 
This project is intended for educational and portfolio purposes. All attack simulations are performed in controlled environments using simulated activity and data.

