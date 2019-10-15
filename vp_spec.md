# VP DID Method Specification
The system aims to provide secure authentication and various payment services based on the DID and Verifiable Claims specificiatons published by the W3C and the Decentralised Identity Foundation. 
VP DID is a decentralized identifier devised to provide a way to uniquely identify a person, an organization.
VP DID document contains information for providing various payment methods among network participants in a decentralized way.
This specification defines how VP blockchain stores DIDs and DID documents, and how to do CRUD operations on DID documents.

## 1. VP DID

### 1.1 VP DID syntax
The namestring that shall identify this DID method is: `vp`.
A DID that uses this method **MUST** begin with the following prefix: `did:vp`. Per the DID specification, this string MUST be in lowercase. 
The remainder of the DID, after the prefix, is specified below.

```
did                 = "did:" method-name ":" method-specific-id
method-name         = vid
method-specific-id  = 40*HEXDIGIT
```

## 2. CURD Method Operation for VP DID

The following section defines the operations supported to manage DIDs.


### 2.1 DID Document Specification

```
{
  "@context": <definition URI of  terminology and a protocol that both systems>,
  "id": <DID that is being registered>,
  "publicKey": {
      "id": <DID that is being registered with key fragment>,
      "type": "RsaVerificationKey2018",
      "owner": <DID that is being registered>,,
      "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
  },

  "authentication": {
        "id": <DID that is being authenticated with key fragment >,
        "type": "Ed25519VerificationKey2018",
        "owner": <DID that is being authenticated>,
        "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
  },

  "service": {
      "id": <DID that is being serviced with key fragment >,
      "type": <Service Name>,
      "serviceEndpoint: <URL for Service Name>
  },

  “created”:  < Created Date of DID Document>
  “updated”:  < Update Date of DID Document>
  “revoked”:  < Revoked Date of DID Document>
}
```

### 2.2 Create (Register)

A DID is created by performing an HTTP POST containing following elements.


#### 2.2.1 Message Format

The message to be delivered consists of a header and a payload.
The header consists of the requestor's identifier and the signature for the payload.
But the Header filed is optional when creating a DID Document.
The payload contains the identifier and document content to be stored in the ledger.
The creation time is automatically included in the document by VP blockchain system.

```
{
“header”: 
{ 
  “requestor”: <Requestor's DID>
  “keyid”:<identifier of key to be used for signing>
  “signature”: <signature over this payload>
}
“payload”:
{
  "@context": <definition URI of terminology and a protocol that both systems>,
  "id": <DID that is being registered>
  "publicKey": {
      "id": <DID that is being registered with key fragment>,
      "type": "RsaVerificationKey2018",
      "owner": <DID that is being registered>,,
      "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
  },

  "authentication": {
     "id": <DID that is being authenticated with key fragment >,
     "type": "Ed25519VerificationKey2018",
     "owner": <DID that is being authenticated>,
     "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
  },
  
  "service": {
     "id": “<DID that is being serviced with key fragment >”,
     "type": “<Service Name>”,
     "serviceEndpoint: “<URL for Service Name>”
  }

}

}
```


#### 2.2.2 Example for creating a DID 

To create a DID, you must submit a transaction that looks like this:

```
POST /create  HTTP/1.1
Host: www.vp.com
Content-Type: application/ld+json
Content-Length: 1062
Accept: application/ld+json, application/json, text/plain, */*
Accept-Encoding: gzip, deflate
{
   "payload":{
      "@context":"https://w3id.org/did/v1",
      "id":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42",
      "publicKey":{
         "id":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42#auth",
         "type":"RsaVerificationKey2018",
         "controller":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42",
         "publicKeyPem":"-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
      },
      "authentication":[
         "did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42#auth"
      ],
      "service":{
         "id":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42#hub",
         "type":"Identity Hub",
         "serviceEndpoint":"https://hub.vp.com/.identity/did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42"
      }
   }
}
```

### 2.3 Read (Resolve)

A DID Document can be read by performing an HTTP POST containing following elements.


#### 2.3.1 Message Format

The overall structure of the message is the same as the message format used by the Create method.
The header field is optional when reading a DID document.
The payload contains the identifier to query.

```
{
  “header”: 
  { 
    “requestor”: <Requestor's DID>
  }
  “payload”: 
  {
    "id": <DID to query>
  }
}
```

**_ TODO _**
The DID method specification MUST specify how a client uses a DID to request a DID Document from the DID Registry, 
including how the client can verify the authenticity of the response.


#### 2.3.2 Example for reading a DID document

To query a DID document, you must submit a transaction that looks like this:

```
POST /read  HTTP/1.1
Host: www.vp.com
Content-Type: application/ld+json
Content-Length: 1062
Accept: application/ld+json, application/json, text/plain, */*
Accept-Encoding: gzip, deflate
{
“payload”: {
    “id” : “did:vid: 3686c73b76b0d9ac183af92ea716c55c4de3de42”
}

}
```

### 2.4 Update (Replace)

A DID document can be replaced by performing an HTTP POST containing following elements.

#### 2.4.1 Message Format

The overall structure of the message is the same as the message format used by the Create method.
The header field is mandatory when updating a DID document.
The update time is automatically included in the document by VP blockchain system.


```
{
  “header”: 
  { 
    “requestor”: <The owner of DID>
    “keyid”:<identifier of key to be used for signing>
    “signature”: <signature over this payload>
  }
  “payload”:
  {
    "@context": <definition URI of terminology and a protocol that both systems>,
    "id": <DID that is being registered>
    "publicKey": {
      "id": <DID that is being registered with key fragment>,
      "type": "RsaVerificationKey2018",
      "owner": <DID that is being registered>,,
      "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
    },

    "authentication": {
        "id": <DID that is being authenticated with key fragment >,
        "type": "Ed25519VerificationKey2018",
        "owner": <DID that is being authenticated>,
        "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
    },
  
    "service": {
       "id": “<DID that is being serviced with key fragment >”,
       "type": “<Service Name>”,
       "serviceEndpoint: “<URL for Service Name>”
    },
  }

}
```

#### 2.4.2 Example for updating a DID document

This example shows how to update the DID document

```
POST /update  HTTP/1.1
Host: www.vp.com
Content-Type: application/ld+json
Content-Length: 1062
Accept: application/ld+json, application/json, text/plain, */*
Accept-Encoding: gzip, deflate

{
   "header":{
      "requestor":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42",
      "keyid":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42#auth",
      "signature":"JKpDCOvDMgqKI0cJDbXRCVEe/DY5F5xTONhF2P2ASkS8eJ4Ez4PhxBuJ85zU4SKlfDUkUjCPgzdlsOhXdKFSCgnq3LBTyvyICCfl5KuT+M0nLPdWLbMC4CTuLivehVSRBxJRKJD/97S7s40FQ/OHeESUAUZSueLlCcoKJrmuTu0="
   },
   "payload":{
      "@context":"https://w3id.org/did/v1",
      "id":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42",
      "publicKey":[
         {
            "id":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42#auth",
            "type":"RsaVerificationKey2018",
            "controller":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42",
            "publicKeyPem":"-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
         },
         {
            "id":"did: vid:123456789abcdefghi#isp",
            "type":"Ed25519VerificationKey2018",
            "controller":"did: vid:123456789abcdefghi",
            "publicKeyBase58":"H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
         }
      ],
      "authentication":[
         "did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42#auth"
      ],
      "service":{
         "id":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42#hub",
         "type":"Identity Hub",
         "serviceEndpoint":"https://hub.vp.com/.identity/did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42"
      }
   }
}
```

### 2.5 Delete (Revoke)

Deletion from the VP Ledger simply means invalidation of the existing DID, and there is no element that prevents the DID owner from creating a new DID. 
In other words, the deleted DID cannot be used.

A DID document can be revoked by performing an HTTP POST containing following elements.

#### 2.5.1 Message Format

The overall structure of the message is the same as the message format used by the Create method.
The header field is mandatory when revoking a DID document.
The payload contains the identifier to revoke.
The revoke time is automatically included in the document by VP blockchain system.

```
{
  “header”: 
  { 
    “requestor”: <The owner of DID>
    “keyid”:<identifier of key to be used for signing>
    “signature”: <signature over this payload>
  }
  “payload”: 
  {
    "id": <DID to revoke>
  }
}
```

#### 2.5.2 Example for revoking a DID 

To revoke the document of the DID, the owner of the DID should send a transaction that looks like this:

```
POST /delete  HTTP/1.1
Host: www.vp.com
Content-Type: application/ld+json
Content-Length: 1062
Accept: application/ld+json, application/json, text/plain, */*
Accept-Encoding: gzip, deflate
{
   "header":{
      "requestor":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42",
      "keyid":"did:vid:3686c73b76b0d9ac183af92ea716c55c4de3de42#auth",   
   "signature":"nV7xYbskOMTL7qWy8AXOgzmPoAaKKgoLD1GSdcv9C6YPv6k8Q1qCE2ZBl4rrBgUrPxLaGrDMMxLMvCZv8Jk9YSwSbP16N5MvJ6kwx5q3EIq/9cN95vFiphuhp8P2ipbIcVF+pFzSMXmjHcJERc6iTz9nvJNvRC6En+CEdQJK6bU="
   },
   "payload":{
      "id":"did:vid: 3686c73b76b0d9ac183af92ea716c55c4de3de42"
   }
}

```

## 3. Security and Privacy Considerations
### 3.1. Security Considerations
VP DID method uses the Redundant Byzantinne Fault Tolerace protocol to reach consensus.
VP DID method conducts all engineering considering at least the folloiwng security risks. They will be discussed more thoroughly in a future version.
 
 Eavesdropping attacks
 Replay attacks
 Message insertion attacks
 Deletion attacks
 Modification attacks
 Man-in-the-middle attacks
 Security of authentication methods
 Residual Risks
 Security assumptions of distributed ledger technology
 Mechanism used to uniquely assign VP DIDs

### 3.2. Privacy Considerations
The VP DID method is architected to ensure reduced correlation risk, increased anonymity, and improved selective sharing for users' self-sovereign identities. This work-in-progress section will discuss, per the requirements listed in the DID Method Specication, the following specific and general areas pertaining to privacy. Terms of use of verifiable credentials will also be discussed, and we will consider whether or not any of the following requirements listed in the Internet Engineering Task Force (IETF) RFC6973, "Privacy Considerations for Internet Protocols", section 5 ("Privacy Threats") apply in a VP DID method-specific way:

 - Surveillance
 - Stored Data Compromise
 - Misattribution
 - Correlation
 - Secondary Use
 - Disclosure
 - Exclusion
 
Additionally, this work-in-progress section will address the following general topics described in the W3C DID Specification:
 - Keeping personally-identifiable information (PII) off-ledger
 - DID Correlation Risks and Pseudonymous DIDs
 - DID Document Correlation Risks
 - Herd Privacy
