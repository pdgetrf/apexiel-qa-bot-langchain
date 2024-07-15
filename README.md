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
        try {
            answer = queryLLM(query, sourceInfo);
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
        }

        response.put("response", answer);

        return ResponseEntity.status(HttpStatus.OK).body(response.toString());
```

### Curl to call the endpoint

```bash
curl "http://$IP_ADDRESS:$PORT/event/rallycross-fest-2024-xnHMrDlZDb/ai/query/chat?query=${QUERY_ENCODED}&stage=${STAGE_ENCODED}"
```
