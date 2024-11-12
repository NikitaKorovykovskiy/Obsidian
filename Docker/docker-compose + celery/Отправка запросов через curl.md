
Если развернуто с python - alpine 

```
apk add --no-cache curl
```

```
curl -X POST http://server:8020/api/send_message/      -H "Content-Type: application/json"      -d '{"phone_number": "89999999999"}'
```
