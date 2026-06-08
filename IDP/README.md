# Sample Internal Developer Platform

## Layered Architecture
This shows the layers in the architecture of the new IDP.

```mermaid
flowchart TB
    subgraph DEV["Developer Environment"]
        direction TD
        GitHub["📁 GitHub\nRepository Management"]
        Agent["🤖 IDE & Agent"]
        SkillRepo["🗂️ Skills Repository"]
        Backstage["🏠 Backstage\nDeveloper Portal"]
        Projen["📐 Projen"]
        Backstage["🏠 Backstage"]
        SonarSec["📡 Sonar\nSecrets Scan · CLI"]
        SonarSAST["📡 Sonar\nQuality · Security · IDE"]
        
    end

    subgraph IL["Sonar : Inner Loop"]
        direction TD
        SQAA["📡 Sonar\nAgentic Analysis"]
        CAG["📡 Sonar\nContext Augmentation"]
    end

    subgraph CI["CI · Build · Test · Supply Chain : Outer Loop"]
        direction TD
        GHActions["🔧 GitHub Actions\nPipeline Orchestration"]
        SonarSCA["📡 Sonar\nSCA · Dependency CVEs"]
        Gitar["🎸 Gitar\nAI Code Review"]
        SQRA["📡 SQRA\nRemediation Agent"]
        CodiumAI["🧪 CodiumAI\nAI Test Generation"]
        Launchable["⚡ Launchable\nAI Test Selection"]
        Sign["Cosign\nArtifact Signing"]
    end

    subgraph REG["Artifact Repositories"]
        direction TD
        Harbor["🐳 Harbor\nContainer Registry"]
        Artifactory["📦 Artifactory\nPackage Proxy"]
    end

    subgraph CD["CD"]
        direction TD
        HITL["🧑‍⚖️ HITL Gate\nHuman-in-the-Loop"]
        ArgoCD["🚀 ArgoCD\nGitOps Delivery"]
        ArgoRollouts["🎯 Argo Rollouts\nProgressive Delivery"]
        Kyverno["📋 Kyverno\nK8s Admission Policy"]
    end

    subgraph OBS["Observability · DORA · AI Metrics"]
        direction TD
        OTel["📡 OpenTelemetry\nTraces · Metrics · Logs"]
        Grafana["📊 Grafana\nDashboards · Alerts"]
        LinearB["📈 LinearB\nEngineering Metrics"]
        Arize["🔭 Arize Phoenix\nAgent Observability"]
        Helicone["💰 Helicone\nLLM Cost Tracking"]
    end

    PROD[("🏭 Production\nKubernetes · mTLS · Zero Trust")]


    DEV -->|"Development"| IL
    IL -->|"Code written, PR opened"| CI
    CI -->|"Artifacts built & signed"| REG    
    REG -->|"Artifacts promoted"| CD
    CD -->|"Artifacts deployed"| OBS
    OBS -->|"Signals to production"| PROD

```

## Developer Workflow
This shows a possible workflow for developers in the new world of agent-centric development.

```mermaid
flowchart TB
    subgraph InnerLoop["AC/DC - Inner Loop"]
        CAG["📡 Sonar\nContext Augmentation"]
        SQAA["📡 Sonar\nAgentic Analysis"]
    end
    subgraph OuterLoop["AC/DC - Outer Loop"]
        Sonar["📡 Sonar\nSAST, SCA, Quality"]
        Gitar["🎸 Gitar\nAI Code Review"]
        SQRA["📡 Sonar\nRemediation Agent"]
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
    Agent --> SQIDE["📡 Sonar for IDE\nConnected Mode"] & InnerLoop
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
    Build --> SonarSBOM["📡 SonarQube Advanced Security\nSBOM"]
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

