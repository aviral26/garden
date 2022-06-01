- [Monolithic Upload blob](#monolithic-upload-blob)

# Monolithic Upload blob

```shell
# Initiate upload
curl \
    -H "Authorization: Basic <base64-username-password>" \
    -X POST \
    --data "" https://localhost/v2/repo/blobs/uploads/

# Complete upload
curl \
    -H "Content-Type : application/octet-stream" \
    -H "Authorization: Basic <base64-username-password>" \
    -X PUT --data "{}" \
    --insecure  "https://<LOCATION>&digest=sha256:44136fa355b3678a1146ad16f7e8649e94fb4fc21fe77e8310c060f61caaff8a"
```
