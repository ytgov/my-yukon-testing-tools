# POSSE VIVVO integration log 2020-11-18

```
echo -n "grant_type=authorization_code&redirect_uri=http://localhost:3000/authorization-code/callback&client_id=bc2e...2609&client_secret=c38f...88af&code=aXZk...jn6s" | http -v --form POST https://yukon.vivvocloud.com/oauth/v2/token
```

still fails. Based on node, it looks like the request_uri *can* now be url-encoded.

```
echo -n "grant_type=authorization_code&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2Fauthorization-code%2Fcallback&client_id=bc2e...2609&client_secret=c38f...88af&code=QcbmEM2D0FZYlsyJz2Ud17YwZe0QW76AMslRJljdhjc.z55AuGbfbyuvyAuJq97M5mlXKa53F0k5HLDw5QKPHzk" | http -v --form POST https://yukon.vivvocloud.com/oauth/v2/token
```

That *did* work.

What about using regular httpie syntax?

```
http -v --form POST https://yukon.vivvocloud.com/oauth/v2/token grant_type=authorization_code "redirect_uri=https://localhost:3000/authorization-code/callback" client_id=bc2e...2609 "client_secret=c38f...88af" code=
```

This worked:

```
leeo@Lees-MacBook-Pro oidc-bash-client % http -v --form POST https://yukon.vivvocloud.com/oauth/v2/token grant_type=authorization_code "redirect_uri=http://localhost:8080/authorization-code/callback" client_id=bc2e...2609 "client_secret=c38f...88af" code=v7PVir9LG4wKKjtjtG6vAOT8SDp2xgyiFjyzN5S2GAo.qfWIV17YuZS3Fcn85PsyxI268iuoQigtBFzbL2n0Yz8
POST /oauth/v2/token HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Content-Length: 295
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: yukon.vivvocloud.com
User-Agent: HTTPie/2.3.0

grant_type=authorization_code&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2Fauthorization-code%2Fcallback&client_id=bc2e...2609&client_secret=c38f...88af&code=v7PVir9LG4wKKjtjtG6vAOT8SDp2xgyiFjyzN5S2GAo.qfWIV17YuZS3Fcn85PsyxI268iuoQigtBFzbL2n0Yz8

HTTP/1.1 200 OK
Cache-Control: no-store
Content-Length: 1554
Content-Type: application/json;charset=UTF-8
Date: Wed, 18 Nov 2020 17:52:29 GMT
Pragma: no-cache

{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQ...iDujQ",
    "expires_in": 1799,
    "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQ...nB0Xg",
    "scope": "openid",
    "token_type": "bearer"
}
```


- WORKS1: grant_type=authorization_code&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2Fauthorization-code%2Fcallback&client_id=bc2e...2609&client_secret=c38f...88af&code=QcbmEM2D0FZYlsyJz2Ud17YwZe0QW76AMslRJljdhjc.z55AuGbfbyuvyAuJq97M5mlXKa53F0k5HLDw5QKPHzk
- WORKS2: grant_type=authorization_code&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2Fauthorization-code%2Fcallback&client_id=bc2e...2609&client_secret=c38f...88af&code=v7PVir9LG4wKKjtjtG6vAOT8SDp2xgyiFjyzN5S2GAo.qfWIV17YuZS3Fcn85PsyxI268iuoQigtBFzbL2n0Yz8
- FAILS2: grant_type=authorization_code&redirect_uri=http%3A%2F%2Flocalhost%3A3000%2Fauthorization-code%2Fcallback&client_id=bc2e...2609&client_secret=c38f...88af&code=u3-rXuVVb7zR_0ccvVpD5ULk31fjzchg5z8LQVVLm60.qtRC9a54vYY_AnGuU-Kj1qqiryXKsuxbBBLKJasAWqI
- FAILS1: grant_type=authorization_code&redirect_uri=https%3A%2F%2Flocalhost%3A3000%2Fauthorization-code%2Fcallback&client_id=bc2e...2609&client_secret=c38f...88af&code=9rVhwqtrra5Up5j8QJeo1N_6Q1nLs92lDU0AoSZaP7g.yNELWklFpbe--sk0VygGJwCKukfiHfEQjf-zD-yu11Q

FAILS2 redirect_uri is wrong -- has an "s" in "https"


Got httpie to work at the token endpoint

```
http -v --form POST https://yukon.vivvocloud.com/oauth/v2/token grant_type=authorization_code "redirect_uri=https://possedev.env.eservices.gov.yk.ca/external/WebUI/CompleteAuthentication" client_id=bc2e...2609 "client_secret=c38f...88af" code=Go2C0xiIBN5sZkyilss_3F3wnZekZOhtaVC2c68FAOQ.XeXlq4HEhK8xcEjGQf7CaiAiB_jw-sdyoFMmpiGUumk
POST /oauth/v2/token HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Content-Length: 324
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: yukon.vivvocloud.com
User-Agent: HTTPie/2.3.0

grant_type=authorization_code&redirect_uri=https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication&client_id=bc2e...2609&client_secret=c38f...88af&code=Go2C0xiIBN5sZkyilss_3F3wnZekZOhtaVC2c68FAOQ.XeXlq4HEhK8xcEjGQf7CaiAiB_jw-sdyoFMmpiGUumk

HTTP/1.1 200 OK
Cache-Control: no-store
Content-Length: 1554
Content-Type: application/json;charset=UTF-8
Date: Wed, 18 Nov 2020 18:10:17 GMT
Pragma: no-cache

{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQ...EEhsA",
    "expires_in": 1799,
    "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQ...5DJEA",
    "scope": "openid",
    "token_type": "bearer"
}
```

What about curl?

```
curl -d grant_type=authorization_code -d "redirect_uri=https://possedev.env.eservices.gov.yk.ca/external/WebUI/CompleteAuthentication" -d client_id=aXZk...jn6s -d "client_secret=c38f...88af" -d code= https://yukon.vivvocloud.com/oauth/v2/token
```

```
curl -d grant_type=authorization_code -d "redirect_uri=https://possedev.env.eservices.gov.yk.ca/external/WebUI/CompleteAuthentication" -d client_id=aXZk...jn6s -d "client_secret=c38f...88af" -d code=X9swB5FfLqpuDSYUAO2IlAg4buNqpZebemkfb3DDWq4.ofa0O8u_o1Pmd-nDHL6FAJf18wR9Ltsc-edLvHme3A0 https://yukon.vivvocloud.com/oauth/v2/token --trace-ascii -
```
fails

Try URL-encoding the redirect_uri

```
curl -d grant_type=authorization_code -d "redirect_uri=https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication" -d client_id=aXZk...jn6s -d "client_secret=c38f...88af" -d code=X9swB5FfLqpuDSYUAO2IlAg4buNqpZebemkfb3DDWq4.ofa0O8u_o1Pmd-nDHL6FAJf18wR9Ltsc-edLvHme3A0 https://yukon.vivvocloud.com/oauth/v2/token --trace-ascii -
```

Wrong client_id

should be bc2e...2609

```
curl -d grant_type=authorization_code -d "redirect_uri=https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication" -d client_id=bc2e...2609 -d "client_secret=c38f...88af" -d code=gUJDeZ41O4m2FP4DjdvTaHo6sagoGvV5DIczqWF69uI.yHd4mPjU9-YE_31qG5szqrWrLAyXGrhAofqmCWh1T9c https://yukon.vivvocloud.com/oauth/v2/token --trace-ascii -
```

and that works!

Even tidier

```
leeo@Lees-MacBook-Pro oidc-bash-client % curl -d grant_type=authorization_code -d "redirect_uri=https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication" -d client_id=bc2e...2609 -d "client_secret=c38f...88af" -d code=r33DZTwFM7NeQ5bn2SEmHNgTHBT7Jm4-Zod8djqjZ0U.BZJ88QC6wYlIABL6C-xGY9k788MN1r5vcTJ-axxT6T4 https://yukon.vivvocloud.com/oauth/v2/token
{"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQ...6sANvA","scope":"openid","token_type":"bearer"}%
```

Spread over multiple lines:

```
curl -d grant_type=authorization_code \
-d "redirect_uri=https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication"  \
-d client_id=bc2e...2609 \
-d "client_secret=c38f...88af" \
-d code=7SZHrXR2YIVN1IFPbp2dY2wWoooGzSMDh2og78Nz7so.ESJKsSjCzvMlmRz595rewA2hQVRpAfRZnG8H8qs4wX4 \
https://yukon.vivvocloud.com/oauth/v2/token
```


```
curl -d grant_type=authorization_code \
-d "redirect_uri=https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication"  \
-d client_id=bc2e...2609 \
-d "client_secret=c38f...88af" \
-d code=7SZHrXR2YIVN1IFPbp2dY2wWoooGzSMDh2og78Nz7so.ESJKsSjCzvMlmRz595rewA2hQVRpAfRZnG8H8qs4wX4 \
https://yukon.vivvocloud.com/oauth/v2/token
{"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJh...KSWg","expires_in":1799,"id_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhd...4hJA","scope":"openid","token_type":"bearer"}%
```

# 2020-11-23

```
curl -d grant_type=authorization_code \
-d "redirect_uri=https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication"  \
-d client_id=bc2e...2609 \
-d "client_secret=c38f...88af" \
-d code=BhRs_4Fv-MkAUtL-W7bVHn0pDbUPbPwPVwavxMBNUNI.Nz_1cbc0mIRFt4Jep04s8iwwcfYJ4aBtLlX_TyRHvPc \
https://yukon.vivvocloud.com/oauth/v2/token
```

That's not working.. hmmm


https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication

https://yukon.vivvocloud.com/oauth/v2/authorize?client_id=bc2e...2609&redirect_uri=https://possedev.env.eservices.gov.yk.ca/external/WebUI/CompleteAuthentication&response_type=code&scope=openid&state=042b6bc479aca70c
 
https://yukon.vivvocloud.com/oauth/v2/authorize?client_id=bc2e...2609&redirect_uri=https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication&response_type=code&scope=openid&state=042b6bc479aca70c

Working flow:

1. Authorize request: https://yukon.vivvocloud.com/oauth/v2/authorize?client_id=bc2e...2609&redirect_uri=https%3A%2F%2Fpossedev.env.eservices.gov.yk.ca%2Fexternal%2FWebUI%2FCompleteAuthentication&response_type=code&scope=openid&state=8084d42962c1ee49
302

2. Token request:
.



