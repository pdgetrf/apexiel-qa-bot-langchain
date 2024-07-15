### Url Endpoint Definition

The new python controller should perform the same checks and return data with the same response format. 

```java
    @GetMapping("/event/{id}/ai/query/chat")
    public ResponseEntity<String> queryOpenAI(
            @PathVariable(value = "id") String eventId,
            HttpServletRequest request,
            @RequestParam(value = "query", defaultValue = "") String query,
            @RequestParam(value = "stage", defaultValue = "stage") String stage
    ) {
        final String pocEvent = "rallycross-fest-2024-xnHMrDlZDb";
        if (eventId == null || !eventId.equals(pocEvent)) {
            logger.info("event {} not supported yet.", eventId);
            return ResponseEntity.status(HttpStatus.NOT_FOUND).body("");
        }

        if (query.trim().isEmpty()) {
            return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("empty query");
        }
        ...
        ...
        ...
        String answer;
        try {
            answer = queryLLM(query, sourceInfo);
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
        }

        response.put("response", answer);

        return ResponseEntity.status(HttpStatus.OK).body(response.toString());
}
```

### Sample Response
This json below is what's in the *answer* variable in the Java code above.

```json
{
  "answer": "Group 1C (Stock AWD, Open 4) and Group 2C (Mod RWD) are racing on Course 2 in the afternoon session on Sunday.",
  "reference": "##### Sunday\n- Session time\n    - Session 1, Morning: 9:45 - 11:45am\n    - Session 2, Midday: 12:15 - 2:15pm\n    - Session 3, Afternoon: 2:45 - 4:45pm\n- Courses\n    - Course 2:\n        - Group 1A (Stock FWD, Mod FWD)\n            - Session 1 (Morning): Work\n            - Session 2 (Midday): Rest\n            - Session 3 (Afternoon): Race\n        - Group 1B (Stock RWD, Prepared AWD)\n            - Session 1 (Morning): Race\n            - Session 2 (Midday): Work\n            - Session 3 (Afternoon): Rest\n        - Group 1C (Stock AWD, Open 4)\n            - Session 1 (Morning): Rest\n            - Session 2 (Midday): Race\n            - Session 3 (Afternoon): Work",
  "categories": "time and schedule"
}
```

### Curl to call the endpoint

```bash
QUERY="what time should spectaator arrive?"
IP_ADDRESS=127.0.0.1
PORT=8082

QUERY_ENCODED=$(printf '%s' "$QUERY" | jq -sRr @uri)
STAGE_ENCODED=$(printf '%s' "$STAGE" | jq -sRr @uri)

# Make the GET request using curl
curl "http://$IP_ADDRESS:$PORT/event/rallycross-fest-2024-xnHMrDlZDb/ai/query/chat?query=${QUERY_ENCODED}&stage=${STAGE_ENCODED}"```
```
