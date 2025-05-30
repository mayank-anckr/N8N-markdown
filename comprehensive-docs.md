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

# Detailed Sequence Diagram

```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant Coordinator
    participant OpenAI
    participant EmailService
    participant DocsService
    participant CalendarService
    participant SlackService
    participant Memory

    User->>Frontend: Submit Chat Message
    Frontend->>API: Send Message
    API->>OpenAI: Process Message
    OpenAI-->>API: Return Parsed Data (type, intent, dates, etc.)
    API->>Coordinator: Process Intent & Type

    alt type == email
        Coordinator->>EmailService: Check Intent

        alt intent == create
            EmailService->>EmailService: Draft New Email
            EmailService-->>Coordinator: Return Draft Status
        else intent == read
            EmailService->>EmailService: Fetch Messages
            EmailService-->>Coordinator: Return Messages
        end

    else type == google docs
        Coordinator->>DocsService: Check Intent

        alt intent == create
            DocsService->>DocsService: Create Document
            DocsService-->>Coordinator: Return Document Info
        else intent == read
            DocsService->>DocsService: Fetch Document
            DocsService-->>Coordinator: Return Document Content
        end

    else type == google calendar
        Coordinator->>CalendarService: Check Intent

        alt intent == create
            CalendarService->>CalendarService: Create Calendar Event
            Note over CalendarService: Use startDate & endDate
            CalendarService-->>Coordinator: Return Event Creation Status
        else intent == read
            CalendarService->>CalendarService: Fetch Events
            CalendarService-->>Coordinator: Return Events List
        end

    else type == slack
        Coordinator->>SlackService: Check Intent

        alt intent == create
            SlackService->>SlackService: Post New Message
            SlackService-->>Coordinator: Return Message Status
        else intent == read
            SlackService->>SlackService: Fetch Messages
            SlackService-->>Coordinator: Return Messages
        end
    end

    Coordinator->>Memory: Store Interaction Context
    Coordinator-->>API: Return Combined Results
    API-->>Frontend: Display Results
    Frontend-->>User: Show Results & Suggestions
```

## Sequence Diagram Explanation

The detailed sequence diagram shows the complete flow of information through the system:

1. **Initial Request**:

   - User submits a chat message through the Frontend
   - The message is sent to the API

2. **Natural Language Processing**:

   - The API sends the message to OpenAI for processing
   - OpenAI returns structured data including type, intent, and other parameters

3. **Request Coordination**:

   - The Coordinator analyzes the parsed data
   - Based on the type (email, google docs, calendar, slack), it routes to the appropriate service

4. **Service-Specific Processing**:

   - Each service handles the request based on the intent (create or read)
   - Email Service: Drafts new emails or retrieves existing ones
   - Google Docs Service: Creates new documents or retrieves existing ones
   - Calendar Service: Creates new events or retrieves scheduled ones
   - Slack Service: Posts new messages or retrieves existing ones

5. **Context Management**:

   - The interaction context is stored in Memory for future reference

6. **Response Flow**:
   - Results from service operations are returned to the Coordinator
   - The Coordinator combines results and sends them back to the API
   - The Frontend displays the results to the User with relevant suggestions
