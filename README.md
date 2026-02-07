- 👋 Hi, I’m Uyi
- 👀 I’m interested in IoT and AI
- 🌱 I’m currently learning AI
- 💞️ I’m looking to collaborate on Anyone
- 📫 How to reach me email
- 😄 Pronouns: Uyi
- ⚡ Fun fact: I was once a kid

<!---
ure5001/ure5001 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

graph TD
    %% -- STYLING --
    classDef user fill:#f9f,stroke:#333,stroke-width:2px;
    classDef frontend fill:#e1f5fe,stroke:#0277bd,stroke-width:2px;
    classDef security fill:#ffcdd2,stroke:#c62828,stroke-width:4px;
    classDef orchestrator fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    classDef agent fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef db fill:#e0e0e0,stroke:#616161,stroke-width:2px,shape:cylinder;
    classDef auth fill:#d1c4e9,stroke:#512da8,stroke-width:2px;

    %% -- NODES --
    User((User)):::user
    
    subgraph Client_Layer ["🖥️ Client Layer (Web/Mobile)"]
        UI[Premium Web Interface]:::frontend
        Login[Login Modal]:::frontend
    end

    subgraph Auth_Layer ["🔐 Authentication"]
        AuthService[Auth Service]:::auth
        OTP_System[Email/SMS OTP]:::auth
    end

    subgraph The_Vault ["🛡️ The Vault (Security Layer)"]
        direction TB
        PII_Redact["PII Redaction"]:::security
        Guardrail["Injection Guard<br/>(Local LLM / Regex)"]:::security
    end

    subgraph Brain ["🧠 Hub & Spoke (LangGraph)"]
        Router{Intent Router}:::orchestrator
        
        subgraph Policy_Spoke ["📘 Policy Spoke"]
            Policy_Agent[Policy Agent]:::agent
            Docs[(Policy Handbook<br/>Markdown/Vector)]:::db
        end
        
        subgraph Banking_Spoke ["💰 Banking Spoke"]
            Bank_Agent[Banking Agent]:::agent
            Bank_DB[(Mock Bank DB)]:::db
        end

        subgraph Support_Spoke ["🎫 Support Spoke"]
            Support_Agent[Support Agent]:::agent
        end
    end

    subgraph Infrastructure ["⚙️ Infrastructure"]
        LLM_Factory{LLM Factory}:::orchestrator
        Ollama[Ollama (Local)]:::agent
        Gemini[Gemini (Cloud)]:::cloud
    end

    %% -- EDGES --
    User -->|Access| Login
    Login -->|Request OTP| AuthService
    AuthService -->|Send Code| OTP_System
    OTP_System -->|Email/SMS| User
    Login -.->|Verified Token| UI

    UI -->|Authenticated Request| PII_Redact
    PII_Redact -->|Sanitized| Guardrail
    
    Guardrail -->|🔴 Attack Detected| UI
    Guardrail -->|🟢 Safe| Router
    
    Router -->|Policy Query| Policy_Agent
    Policy_Agent <-->|Read| Docs
    
    Router -->|Transfer/Balance| Bank_Agent
    Bank_Agent <-->|Query| Bank_DB

    Router -->|Help/Ticket| Support_Agent
    
    %% LLM Connections
    Policy_Agent -.-> LLM_Factory
    Bank_Agent -.-> LLM_Factory
    Support_Agent -.-> LLM_Factory
    Router -.-> LLM_Factory
    Guardrail -.-> LLM_Factory
    
    LLM_Factory -.->|Config: Local| Ollama
    LLM_Factory -.->|Config: Cloud| Gemini
