## 📊 Rate Limiting Logic

The user data is limited to avoid abuse by users. It is cached by Redis once rate limit reached to keep data filled. 
<br />
The current algorithm that is employed is the Token Bucket algorithm, look at diagram to understand how it is implemented in DailySAT.

```mermaid
flowchart 
    Start["Start"] --> ReceiveRequest["Receive Request from Middleware"]
    ReceiveRequest --> CheckTokenAvailability{"Is There Tokens Left?"}
    CheckTokenAvailability -- No --> RejectRequest["Reject Request"]
    CheckTokenAvailability -- Yes --> ConsumeToken["Delete one Token"]
    ConsumeToken --> ProcessRequest["Process Request"]
    ProcessRequest --> CheckRefill{"Check Timestamp with Current Timestamp. Is it Time to Refill Tokens?"}
    RejectRequest --> CheckRefill
    CheckRefill -- Yes --> RefillTokens["Refill Tokens at Current Rate"]
    CheckRefill -- No --> ContinueWithCurrentTokens["Continue with Current Token Count"]

    RefillTokens --> End["End"]
    ContinueWithCurrentTokens --> End
```
