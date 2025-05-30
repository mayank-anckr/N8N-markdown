# System Architecture Flow Chart

```mermaid
flowchart TD
    A[Trigger: Chat Message Received] --> B[OpenAI Processing]
    B --> C[Extract: type, intent, startDate, endDate, message, link]
    C --> D[Parse Response]
    D --> E{Switch: Check Type}

    %% Email Branch
    E -->|type == email| F{Check Intent}
    F -->|intent == create| G[Email: Draft New Message]
    F -->|intent == read| H[Email: Read Messages]

    %% Google Docs Branch
    E -->|type == google docs| I{Check Intent}
    I -->|intent == create| J[Google Docs: Create Document]
    I -->|intent == read| K[Google Docs: Read Document]

    %% Google Calendar Branch
    E -->|type == google calendar| L{Check Intent}
    L -->|intent == create| M[Google Calendar: Create Event]
    L -->|intent == read| N[Google Calendar: View Events]

    %% Slack Branch
    E -->|type == slack| O{Check Intent}
    O -->|intent == create| P[Slack: Post Message]
    O -->|intent == read| Q[Slack: Read Messages]

    %% Final Actions
    G --> R[Complete Email Action]
    H --> R
    J --> S[Complete Google Docs Action]
    K --> S
    M --> T[Complete Calendar Action]
    N --> T
    P --> U[Complete Slack Action]
    Q --> U
```

## Workflow Description

1. The process begins with a **Chat Message Received** trigger
2. The message is sent to **OpenAI** for processing
3. OpenAI extracts key information:
   - Type (email, google docs, google calendar, slack)
   - Intent (create, read)
   - Additional data (startDate, endDate, message, link)
4. The system parses the response from OpenAI
5. A switch statement checks the **type** of request
6. Based on type, the flow branches to the appropriate service
7. For each service, the **intent** is checked to determine the specific action:
   - For **Email**: Draft new emails or read existing emails
   - For **Google Docs**: Create new documents or read existing ones
   - For **Google Calendar**: Create new events or view scheduled events
   - For **Slack**: Post new messages or read existing messages
8. Each branch completes its respective action within the target application
