# SOAP Bank (Spring Boot + Spring-WS)

A simple **SOAP 1.1 “bank” web service** built with **Spring Boot** and **Spring Web Services (Spring-WS)** using a **contract-first** approach (XSD → WSDL + JAXB-generated classes).

## Features

Operations exposed by the SOAP service (see `src/main/resources/bank.xsd`):

- **GetAccount**: fetch account details by `accountId`
- **Deposit**: add money to an account (returns the new balance)
- **Withdraw**: debit money from an account (returns the updated account)

In-memory sample accounts are initialized at startup:
- `A100` (Alice) — `150.00 TND`
- `B200` (Bob) — `80.50 TND`

## Requirements

- Java **17**
- Maven

## Build & Run

```bash
mvn clean package
mvn spring-boot:run
```

The SOAP endpoint is exposed under:

- **SOAP endpoint**: `http://localhost:8080/ws`
- **WSDL**: `http://localhost:8080/ws/bank.wsdl`

## Testing with Postman (SOAP 1.1)

### Request setup

- Method: **POST**
- URL: `http://localhost:8080/ws`
- Header: `Content-Type: text/xml; charset=utf-8`
- Body: **raw** (XML)

### GetAccount

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                  xmlns:ban="http://example.com/bank">
  <soapenv:Header/>
  <soapenv:Body>
    <ban:GetAccountRequest>
      <ban:accountId>A100</ban:accountId>
    </ban:GetAccountRequest>
  </soapenv:Body>
</soapenv:Envelope>
```

### Deposit

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                  xmlns:ban="http://example.com/bank">
  <soapenv:Header/>
  <soapenv:Body>
    <ban:DepositRequest>
      <ban:accountId>A100</ban:accountId>
      <ban:amount>20.00</ban:amount>
    </ban:DepositRequest>
  </soapenv:Body>
</soapenv:Envelope>
```

### Withdraw

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                  xmlns:ban="http://example.com/bank">
  <soapenv:Header/>
  <soapenv:Body>
    <ban:WithdrawRequest>
      <ban:accountId>A100</ban:accountId>
      <ban:amount>10.00</ban:amount>
    </ban:WithdrawRequest>
  </soapenv:Body>
</soapenv:Envelope>
```

### Fault example (amount <= 0)

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                  xmlns:ban="http://example.com/bank">
  <soapenv:Header/>
  <soapenv:Body>
    <ban:DepositRequest>
      <ban:accountId>A100</ban:accountId>
      <ban:amount>-5</ban:amount>
    </ban:DepositRequest>
  </soapenv:Body>
</soapenv:Envelope>
```

## Included Postman collection

A Postman collection is available in:

- `tests/postman_tests.json`

You can import it into Postman and adjust `{{baseUrl}}` if needed (default is `http://localhost:8080`).

## Notes

- The project follows **contract-first** SOAP development:
  - Contract: `src/main/resources/bank.xsd`
  - WSDL is generated/exposed by Spring-WS at runtime (`/ws/bank.wsdl`)
  - JAXB classes are generated during the Maven build (see `jaxb2-maven-plugin` in `pom.xml`)
