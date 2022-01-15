---
layout: post
title: API Rate Limiting in FastAPI with Redis
description: 
comments: False
toc: true
categories: [Python, FastAPI, Redis, Bangla]
---

## API-Key Based Authentication in FastApi

```python
API_KEY_NAME = "x-api-key"
API_KEY_HEADER = APIKeyHeader(name=API_KEY_NAME, auto_error=False)


async def authenticate_api_key(api_key: str = Security(API_KEY_HEADER), 
                            authenticator: APIKeyAuthenticator = Depends(DummyAuthenticator)) -> NoReturn:
    try:                            
        await authenticator.authenticate(api_key)        
    except AuthenticationException as excep:
        logger.exception(excep)
        raise
    except Exception as excep:
        logger.exception(excep)
        raise ServiceException()

```

### Adding Authentication in Router

TODO

### Zooming in DummyAuthenticator

TODO


### Resources:

1 - [ApiKey Header documentation #142](https://github.com/tiangolo/fastapi/issues/142)