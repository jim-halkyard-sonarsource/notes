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
        Backstage["🏠 Backstage\nDeveloper Portal"]
        Agent["🤖 Agent\nAI Coding Assistant"]
        Devcontainers["⚡ Devcontainers\nDev Environments"]
        Projen["📐 Projen\nProject Scaffolding"]
        Sourcegraph["🔎 Sourcegraph\nCode Intelligence"]
    end

    subgraph SCM["② Source Control · Code Review · SAST"]
        direction TD
        GitHub["📁 GitHub\nRepo"]
        SonarSec["🕵️ Sonar\nSecrets Scan · CLI"]
        SonarSAST["📡🛡️ Sonar\nSAST · IDE & CI"]
        SonarSCA["📡✅ Sonar\nSCA · Dependency CVEs"]
        Gitar["🎸 Gitar\nAI Code Review"]
    end

    subgraph CICD["③ CI/CD · Build · Test · Supply Chain"]
        direction TD
        GHActions["🔧 GitHub Actions\nPipeline Orchestration"]
        CodiumAI["🧪 CodiumAI\nAI Test Generation"]
        Launchable["⚡ Launchable\nAI Test Selection"]
        SonarSBOM["🏷️ SonarQube Advanced Security\nSBOM"]
        Sign["Cosign\nArtifact Signing"]
    end

    subgraph REG["④ Artifact · Model · Skill Registries"]
        direction TD
        Harbor["🐳 Harbor\nContainer Registry"]
        Artifactory["📦 Artifactory\nPackage Proxy"]
        MLflow["🧠 MLflow\nModel Registry"]
        SkillRepo["🗂️ Skills Repository\nMCP / OpenAPI Catalog"]
        ProtectAI["🛡️ Protect AI\nModel Risk Scan"]
    end

    subgraph SEC["⑤ Security · Secrets · Identity · Compliance"]
        direction TD
        Vault["🔑 Vault\nSecrets Management"]
        SPIRE["🔒 SPIRE\nWorkload Identity"]
        OPA["📋 OPA\nPolicy-as-Code"]
        Lakera["🧠 Lakera Guard\nPrompt Injection"]
        Falco["🦅 Falco\nRuntime Security"]
        Drata["📜 Drata\nCompliance Automation"]
    end

    subgraph AI["⑥ AI Orchestration · LLM Gateway · Agent Runtime"]
        direction TD
        LiteLLM["🌐 LiteLLM\nLLM Gateway"]
        LangGraph["🔗 LangGraph\nAgent Orchestration"]
        Langfuse["👁️ Langfuse\nPrompt Versioning"]
        Ragas["🧬 Ragas\nEval Framework"]
        HITL["🧑‍⚖️ HITL Gate\nHuman-in-the-Loop"]
    end

    subgraph GITOPS["⑦ GitOps Delivery · Progressive Rollout · IaC"]
        direction TD
        ArgoCD["🚀 ArgoCD\nGitOps Delivery"]
        ArgoRollouts["🎯 Argo Rollouts\nProgressive Delivery"]
        Pulumi["☁️ Pulumi\nIaC · AI-Assisted"]
        Crossplane["🔧 Crossplane\nSelf-Service Infra"]
        Kyverno["📋 Kyverno\nK8s Admission Policy"]
    end

    subgraph OBS["⑧ Observability · DORA · AI Metrics"]
        direction TD
        OTel["📡 OpenTelemetry\nTraces · Metrics · Logs"]
        Grafana["📊 Grafana\nDashboards · Alerts"]
        LinearB["📈 LinearB\nEngineering Metrics"]
        Arize["🔭 Arize Phoenix\nAgent Observability"]
        Helicone["💰 Helicone\nLLM Cost Tracking"]
    end

    PROD[("🏭 Production\nKubernetes · mTLS · Zero Trust")]


    DEV -->|"① Code written, PR opened"| SCM
    SCM -->|"② Gates passed, merge"| CICD
    CICD -->|"③ Artifacts built & signed"| REG    
    REG -->|"④ Artifacts promoted"| SEC
    SEC -->|"⑤ Identity + policy enforced"| AI
    AI -->|"⑥ Agent tasks approved"| GITOPS
    GITOPS -->|"⑦ Deployed to cluster"| OBS
    OBS -->|"⑧ Signals to production"| PROD
```