# Sample Internal Developer Platform

## Developer Workflow

```mermaid
flowchart TB
    subgraph InnerLoop["Sonar MCP - Inner Loop"]
        CAG["🧰 Context Augmentation"]
        SQAA["🔒 Agentic Analysis"]
    end
    subgraph OuterLoop["SonarQube - Outer Loop"]
        Sonar["📡✅ SAST, SCA, Quality"]
        Gitar["🎸 Gitar\nAI Code Review"]
        SQRA["📋 Remediation Agent"]
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
    Agent --> SQIDE["🖥️⚡SQ for IDE\nConnected Mode"] & InnerLoop
    SQIDE --> Agent
    SkillRepo["🗂️ Skills Repository"] --> Agent
    Agent -- scaffolds via --> Projen["📐 Projen"]
    Agent -- discovers services via --> Backstage["🏠 Backstage"]
    InnerLoop --> Agent
    Agent -- prompts --> LLM["🧠💻 LLM"]
    LLM -- returns code --> Agent
    Agent -- opens PR to --> GitHub["📁 GitHub"]
    GitHub --> GHActions["🔧 GitHub Actions\nCI Pipeline"]
    GHActions --> OuterLoop & CodiumAI["🧪 CodiumAI\nGenerate Tests"] & Launchable["⚡ Launchable\nSelect Tests"] & SonarSBOM["🏷️ SQAS & Cosign \nSBOM & Sign"]
    OuterLoop --> GHActions
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

```mermaid
graph TD
  
    subgraph DEV["① Developer Experience Layer"]
        Backstage["🏠 Backstage\nDeveloper Portal"]
        Copilot["🤖 GitHub Copilot\nAI Coding Assistant"]
        Devcontainers["⚡ Devcontainers\nDev Environments"]
        Projen["📐 Projen\nProject Scaffolding"]
        Sourcegraph["🔎 Sourcegraph\nCode Intelligence"]
    end

    subgraph SCM["② Source Control · Code Review · SAST"]
        GitHub["📁 GitHub Enterprise\nRepo · Branch Protection"]
        Sonar["🕵️ Sonar\nSecrets Scan · CLI"]
        Sonar["📡🛡️ Sonar\nSAST · IDE & CI"]
        Sonar["📡✅ Sonar\nSCA · Dependency CVEs"]
        Gitar["🎸 Gitar\nAI ReviewA · I Code Review"]
    end

    subgraph CICD["③ CI/CD · Build · Test · Supply Chain"]
        GHActions["🔧 GitHub Actions\nPipeline Orchestration"]
        CodiumAI["🧪 CodiumAI\nAI Test Generation"]
        Launchable["⚡ Launchable\nAI Test Selection"]
        SyftCosign["🏷️ Syft + Cosign\nSBOM · Artifact Signing"]
    end

    subgraph REG["④ Artifact · Model · Skill Registries"]
        Harbor["🐳 Harbor\nContainer Registry"]
        Artifactory["📦 Artifactory\nPackage Proxy"]
        MLflow["🧠 MLflow\nModel Registry"]
        SkillReg["🗂️ Skill Registry\nMCP / OpenAPI Catalog"]
        ProtectAI["🛡️ Protect AI\nModel Risk Scan"]
    end

    subgraph SEC["⑤ Security · Secrets · Identity · Compliance"]
        Vault["🔑 Vault\nSecrets Management"]
        SPIRE["🔒 SPIRE\nWorkload Identity"]
        OPA["📋 OPA\nPolicy-as-Code"]
        Lakera["🧠 Lakera Guard\nPrompt Injection"]
        Falco["🦅 Falco\nRuntime Security"]
        Drata["📜 Drata\nCompliance Automation"]
    end

    subgraph AI["⑥ AI Orchestration · LLM Gateway · Agent Runtime"]
        LiteLLM["🌐 LiteLLM\nLLM Gateway"]
        LangGraph["🔗 LangGraph\nAgent Orchestration"]
        Langfuse["👁️ Langfuse\nPrompt Versioning"]
        Ragas["🧬 Ragas\nEval Framework"]
        HITL["🧑‍⚖️ HITL Gate\nHuman-in-the-Loop"]
    end

    subgraph GITOPS["⑦ GitOps Delivery · Progressive Rollout · IaC"]
        ArgoCD["🚀 ArgoCD\nGitOps Delivery"]
        ArgoRollouts["🎯 Argo Rollouts\nProgressive Delivery"]
        Pulumi["☁️ Pulumi\nIaC · AI-Assisted"]
        Crossplane["🔧 Crossplane\nSelf-Service Infra"]
        Kyverno["📋 Kyverno\nK8s Admission Policy"]
    end

    subgraph OBS["⑧ Observability · DORA · AI Metrics"]
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