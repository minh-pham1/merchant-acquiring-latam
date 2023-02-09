---
tags: [Getting Started, Payments, Error codes]
---

# Códigos de erro

## AppKey ausente na solicitação, 400 BAD REQUEST

```json
{
    "clientRequestId": "12345",
    "responseType": "BadRequest",
    "error": {
        "code": "400",
        "message": "The request is invalid or not properly formed"
    }
}
```

## Chave de API inválida, 401 não autorizado

```json
{
    "clientRequestId": "12345",
    "responseType": "Unauthorized",
    "error": {
        "code": "401",
        "message": "Invalid API key"
    }
} 
```

## HMAC inválido, 403 proibido

```json
{
    "clientRequestId": "4835065",
    "responseType": "Forbidden",
    "error": {
        "code": "403",
        "message": "Message Signature validation error"
    }
} 
```

## HMAC expirado, 403 proibido

```json
{
    "clientRequestId": "551228",
    "responseType": "Forbidden",
    "error": {
        "code": "403",
        "message": "Message Signature has expired"
    }
} 
```

## URL inválido ou parâmetro de consulta inválido para /payment-url, 404 NotFound

```json
{
    "clientRequestId": "2952405",
    "responseType": "NotFound",
    "error": {
        "code": "404",
        "message": "The requested operation failed because a resource associated with the request could not be found"
    }
}
```

## Token OAuth inválido ou expirado, 401 não autorizado

```json
{
    "clientRequestId": "3091653",
    "responseType": "Unauthorized",
    "error": {
        "code": "401",
        "message": "The token is invalid. It may have expired, or is maybe incorrect"
    }
} 
```

## Assinatura da mensagem ou cabeçalho de autorização ausente, 401 não autorizado

```json
{
    "responseType": "Unauthorized",
    "error": {
        "code": "401",
        "message": "Not authorized to access the resource"
    }
} 
```

## Erro padrão para exceções não tratadas, 500 ServerError

```json
{
    "clientRequestId": "3091653",
    "responseType": "ServerError",
    "error": {
        "code": "500",
        "message": "The request failed due to an internal error"
    }
} 
```

---

## Veja também

- [Tipos de Requisição](?path=docs/portuguese/payments/3-1-tipos-requisição.md)
- [Métodos de pagamento](?path=docs/portuguese/payments/3-2-metodos-pagamento.md)
- [Tipos de pagamento](?path=docs/portuguese/payments/3-3-tipos-pagamento.md)
- [Post-authorisation & Returns](?path=docs/portuguese/payments/3-4-post-auth.md)
- [3-D Secure](?path=docs/portuguese/payments/3-5-3d-secure.md)
- [Verificação de cartão](?path=docs/portuguese/payments/3-6-verificação-cartão.md)
- [Conversão de moeda](?path=docs/portuguese/payments/3-7-conversão-moeda.md)
- [Redirecionamento controlado](?path=docs/portuguese/payments/3-8-redirecionamento-controlado.md)
- [Ordens](?path=docs/portuguese/payments/3-9-ordens.md)
- [Link de Pagamento](?path=docs/portuguese/payments/3-10-link-pagamento.md)
- [Pagamento recorrente](?path=docs/portuguese/payments/3-11-pagamento-recorrente.md)
- [Tokenização](?path=docs/portuguese/payments/3-12-tokenização.md)

---
