# Sample Internal Developer Platform

## Developer Workflow
This shows a possible workflow for developers in the new world of agent-centric development.

```mermaid
flowchart TB
    subgraph InnerLoop["AC/DC - Inner Loop"]
        CAG["🧰 Sonar\nContext Augmentation"]
        SQAA["🔒 Sonar\nAgentic Analysis"]
    end
    subgraph OuterLoop["AC/DC - Outer Loop"]
        Sonar["📡✅ Sonar\nSAST, SCA, Quality"]
        Gitar["🎸 Gitar\nAI Code Review"]
        SQRA["📋 Sonar\nRemediation Agent"]
    end
    subgraph repo["repo"]
        Harbor["🐳 Harbor"]
        Artifactory["📦 Artifactory"]
    end
    subgraph CD["CD"]
        HITL["🧑‍⚖️ HITL\nHuman Approval"]
        Ragas["🧬 Ragas\nEval Gate"]
        ArgoCD["🚀 ArgoCD\nGitOps Sync"]
        ArgoRollouts["🎯 Argo Rollouts\nCanary Deploy"]
        Kyverno["📋 Kyverno\nAdmission Policy"]
    end

    Dev(["👨‍💻 Developer"]) -- uses --> Agent["🤖 IDE & Agent"]
    Agent --> SQIDE["🖥️⚡ Sonar for IDE\nConnected Mode"] & InnerLoop
    SQIDE --> Agent
    SkillRepo["🗂️ Skills Repository"] --> Agent
    Agent -- scaffolds via --> Projen["📐 Projen"]
    Agent -- discovers services via --> Backstage["🏠 Backstage"]
    InnerLoop --> Agent
    Agent -- prompts --> LLM["🧠💻 LLM"]
    LLM -- returns code --> Agent
    Agent -- opens PR to --> GitHub["📁 GitHub"]
    GitHub --> GHActions["🔧 GitHub Actions\nCI Pipeline"]
    GHActions --> OuterLoop & CodiumAI["🧪 CodiumAI\nGenerate Tests"] & Launchable["⚡ Launchable\nSelect Tests"] & Build["Build"] 
    Build --> SonarSBOM["🏷️ SonarQube Advanced Security\nSBOM"]
    OuterLoop --> GHActions
    Build --> Sign["Cosign\nArtifact Signing"]
    Sign --> repo
    SonarSBOM --> repo
    repo --> CD
    CD --> PROD[("🏭 Production")]
    PROD --> OTel["📡 OpenTelemetry"]
    OTel --> Grafana["📊 Grafana"] & Arize["🔭 Arize Phoenix"] & Helicone["💰 Helicone"] & LinearB["📈 LinearB"]
    Ragas -- "high-risk action" --> HITL
    Ragas -- "low-risk action" --> ArgoCD
    HITL -- approved --> ArgoCD
    ArgoCD --> ArgoRollouts
    ArgoRollouts --> Kyverno
```

## Layered Architecture
This shows the layers in the architecture of the new IDP.

#### TODO:
* Remove tools mentioned here that are not mentioned above
* Move Sonar and other tools to the appropriate level
* Simplify the number of levels

```mermaid
flowchart TB
    subgraph DEV["① Developer Experience Layer"]
        direction TD
        GitHub["📁 GitHub\nRepo"]
        Agent["🤖 Agent\nAI Coding Assistant"]
        SkillRepo["🗂️ Skills Repository\nMCP / OpenAPI Catalog"]
        SonarSec["🕵️ Sonar\nSecrets Scan · CLI"]
        SonarSAST["📡✅ Sonar\nQuality · Security · IDE"]
        Backstage["🏠 Backstage\nDeveloper Portal"]
        Devcontainers["⚡ Devcontainers\nDev Environments"]
        Projen["📐 Projen\nProject Scaffolding"]
    end

    subgraph CI["② CI · Build · Test · Supply Chain"]
        direction TD
        GHActions["🔧 GitHub Actions\nPipeline Orchestration"]
        SonarSCA["📡✅ Sonar\nSCA · Dependency CVEs"]
        Gitar["🎸 Gitar\nAI Code Review"]
        CodiumAI["🧪 CodiumAI\nAI Test Generation"]
        Launchable["⚡ Launchable\nAI Test Selection"]
        Sign["Cosign\nArtifact Signing"]
    end

    subgraph REG["③ Artifact Repositories"]
        direction TD
        Harbor["🐳 Harbor\nContainer Registry"]
        Artifactory["📦 Artifactory\nPackage Proxy"]
    end

    subgraph CD["④ CD"]
        direction TD
        HITL["🧑‍⚖️ HITL Gate\nHuman-in-the-Loop"]
        ArgoCD["🚀 ArgoCD\nGitOps Delivery"]
        ArgoRollouts["🎯 Argo Rollouts\nProgressive Delivery"]
        Kyverno["📋 Kyverno\nK8s Admission Policy"]
    end

    subgraph OBS["⑤ Observability · DORA · AI Metrics"]
        direction TD
        OTel["📡 OpenTelemetry\nTraces · Metrics · Logs"]
        Grafana["📊 Grafana\nDashboards · Alerts"]
        LinearB["📈 LinearB\nEngineering Metrics"]
        Arize["🔭 Arize Phoenix\nAgent Observability"]
        Helicone["💰 Helicone\nLLM Cost Tracking"]
    end

    PROD[("🏭 Production\nKubernetes · mTLS · Zero Trust")]


    DEV -->|"Code written, PR opened"| CI
    CI -->|"Artifacts built & signed"| REG    
    REG -->|"Artifacts promoted"| CD
    CD -->|"Artifacts deployed"| OBS
    OBS -->|"Signals to production"| PROD

```