<h1 align="center">jwt.mq</h1>

A JWT decoder implemented as an [mq](https://github.com/harehare/mq) module.

> **Note:** This module decodes JWT tokens for inspection only. It does **not** verify signatures. Never use it for authentication.

## Features

- Decode header and payload from any JWT token
- Base64url decoding with automatic padding
- Check token expiry against a supplied timestamp
- Convenience accessors for header and payload

## Installation

```sh
cp jwt.mq ~/.local/mq/config/
```

### HTTP Import

```sh
mq -I raw 'import "github.com/harehare/jwt.mq" | jwt::jwt_decode(.)' token.txt
```

## API

| Function | Description |
|---|---|
| `jwt_decode(token)` | Returns `{ header, payload, signature }` |
| `jwt_payload(token)` | Returns only the payload claims dict |
| `jwt_header(token)` | Returns only the header dict |
| `jwt_expired(token, now)` | Returns `true` if `exp` claim is before `now` (Unix timestamp) |

## Example

```sh
TOKEN="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"

# Decode the full token
echo "$TOKEN" | mq -I raw 'import "jwt" | jwt::jwt_decode(.)'
# => {"header":{"alg":"HS256","typ":"JWT"},"payload":{"sub":"1234567890","name":"John Doe","iat":1516239022},"signature":"..."}

# Extract a claim
echo "$TOKEN" | mq -I raw 'import "jwt" | jwt::jwt_payload(.)["name"]'
# => "John Doe"

# Check algorithm
echo "$TOKEN" | mq -I raw 'import "jwt" | jwt::jwt_header(.)["alg"]'
# => "HS256"

# Check expiry (supply current Unix timestamp)
echo "$TOKEN" | mq -I raw 'import "jwt" | jwt::jwt_expired(., 1700000000)'
# => true
```

## License

MIT

