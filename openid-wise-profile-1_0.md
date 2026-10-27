---
stand_alone: true
ipr: none
cat: std # Check
submissiontype: IETF
wg: OpenID Shared Signals

docname: openid-wise-profile-1_0

title: "OpenID WISE Profile Specification 1.0 - draft 04"
abbrev: wiseset
lang: en
kw:
 - security events
 - workload identity
 - shared signals
 - SET

author:
- ins: J. Lombardo
  name: Jeff Lombardo
  org: Amazon Web Services
  email: jeff@authnopuz.xyz
- ins: D. Sneeggen
  name: Dag Sneeggen
  org: Dendro Systems
  email: dag@dendro.systems
- ins: S. O'Dell
  name: Sean O'Dell
  org: CVS Health
  email: sean.odell@cvshealth.com
- ins: P. Kasselman
  name: Pieter Kasselman
  org: Defakto Security
  email: pieter@defakto.security

normative:
  RFC5646:
  RFC7516:
  RFC7517:
  RFC7518:
  RFC7519:
  RFC7523:
  RFC8126:
  RFC8417:
  RFC8705:
  RFC9325:
  RFC9493:
  SSF:
    title: "OpenID Shared Signals Framework Specification 1.0"
    target: https://openid.net/specs/openid-sharedsignals-framework-1_0.html
    author:
      - ins: A. Tulshibagwale
        name: Atul Tulshibagwale
      - ins: T. Cappalli
        name: Tim Cappalli
      - ins: M. Scurtescu
        name: Marius Scurtescu
      - ins: A. Backman
        name: Annabelle Backman
      - ins: J. Bradley
        name: John Bradley
      - ins: S. Miel
        name: Shayne Miel
    date: 2025
  CAEP:
    title: "OpenID Continuous Access Evaluation Profile 1.0"
    target: https://openid.net/specs/openid-caep-1_0.html
    author:
      - ins: T. Cappalli
        name: Tim Cappalli
      - ins: A. Tulshibagwale
        name: Atul Tulshibagwale
    date: 2025
  RISC:
    title: "OpenID RISC Profile Specification 1.0"
    target: https://openid.net/specs/openid-risc-1_0-final.html
    author:
      - ins: M. Scurtescu
        name: Marius Scurtescu
      - ins: A. Backman
        name: Annabelle Backman
      - ins: P. Hunt
        name: Phil Hunt
      - ins: J. Bradley
        name: John Bradley
      - ins: S. Bounev
        name: Stan Bounev
      - ins: A. Tulshibagwale
        name: Atul Tulshibagwale
    date: 2025
  WIMSE-ARCH:
    title: "Workload Identity in a Multi System Environment (WIMSE) Architecture"
    target: https://datatracker.ietf.org/doc/draft-ietf-wimse-arch/
    author:
      - ins: J. Salowey
        name: Joseph Salowey
      - ins: Y. Rosomakho
        name: Yaroslav Rosomakho
      - ins: H. Tschofenig
        name: Hannes Tschofenig
    date: 2026
  WIMSE-ID:
    title: "Workload Identifier"
    target: https://datatracker.ietf.org/doc/draft-ietf-wimse-identifier/
    author:
      - ins: Y. Rosomakho
        name: Yaroslav Rosomakho
      - ins: J. Salowey
        name: Joe Salowey
    date: 2026
  WIMSE-CRED:
    title: "WIMSE Workload Credentials"
    target: https://datatracker.ietf.org/doc/draft-ietf-wimse-workload-creds/
    author:
      - ins: B. Campbell
        name: Brian Campbell
      - ins: J. Salowey
        name: Joe Salowey
      - ins: A. Schwenkschuster
        name: Arndt Schwenkschuster
      - ins: Y. Sheffer
        name: Yaron Sheffer
      - ins: Y. Rosomakho
        name: Yaroslav Rosomakho
    date: 2026
  WPT:
    title: "WIMSE Workload Proof Token"
    target: https://datatracker.ietf.org/doc/draft-ietf-wimse-wpt/
    author:
      - ins: B. Campbell
        name: Brian Campbell
      - ins: A. Schwenkschuster
        name: Arndt Schwenkschuster
    date: 2026

informative:
  RFC6920:
  SPIFFE:
    title: "Secure Production Identity Framework for Everyone"
    target: https://spiffe.io/docs/latest/spiffe-specs/spiffe/
    date: 2024
  AIMS:
    title: "AI Identity Management System"
    target: https://datatracker.ietf.org/doc/draft-ietf-wimse-aims/
    author:
      - ins: P. Kasselman
        name: Pieter Kasselman
      - ins: J. Lombardo
        name: Jean-François Lombardo
      - ins: Y. Rosomakho
        name: Yaroslav Rosomakho
      - ins: B. Campbell
        name: Brian Campbell
      - ins: N. Steele
        name: Nick Steele
      - ins: A. Parecki
        name: Aaron Parecki
    date: 2026
  CIMD:
    title: "OAuth Client ID Metadata Document"
    target: https://datatracker.ietf.org/doc/draft-ietf-oauth-client-id-metadata-document/
    author:
      - ins: A. Parecki
        name: Aaron Parecki
      - ins: E. Smith
        name: Emelia Smith
    date: 2026
  VEX:
    title: "Minimum Requirements for Vulnerability Exploitability eXchange (VEX)"
    target: https://www.cisa.gov/resources-tools/resources/minimum-requirements-vulnerability-exploitability-exchange-vex
    date: 2023
  IANA.JOSE:
    title: "JSON Object Signing and Encryption (JOSE)"
    target: https://www.iana.org/assignments/jose
    author:
      - org: IANA
    date: false

--- abstract

This document defines the Workload Identity Security Events (WISE) profile, a set of Security Event Token (SET) event types for signaling security-relevant state changes related to workload identities. WISE builds on the SET framework defined in {{RFC8417}} and the Shared Signals Framework {{SSF}} to enable trust domains and identity infrastructure components to communicate workload identity lifecycle events, credential and key management events, trust material changes, and posture evaluation events.

WISE complements the existing RISC and CAEP profiles by addressing the non-human identity domain, specifically workload-to-workload authentication and the workload identity lifecycle as described in the WIMSE architecture {{WIMSE-ARCH}}.

--- middle

# Introduction

Modern distributed systems rely on workloads, software entities executing for a specific purpose, to deliver services. These workloads include microservices, containers, virtual machines, serverless functions, and increasingly, AI agents operating autonomously or on behalf of users.

The WIMSE architecture {{WIMSE-ARCH}} establishes the foundational model for workload identity: a trust domain, typically governed by a single authority, provisions cryptographic credentials to workloads that allow them to authenticate to one another. The credentials are short-lived by design, binding a workload identifier to key material through either Workload Identity Tokens (WIT) at the application layer or Workload Identity Certificates (WIC) at the transport layer, as defined in {{WIMSE-CRED}}.

The emergence of AI agents as a new category of workload, as described in {{AIMS}}, introduces additional security coordination requirements. AI agents interact with tools, services, and other agents across trust domain boundaries, often autonomously. Like any workload, they require identifiers, credentials, and posture evaluation before credentials are issued. The security events defined in this specification apply equally to traditional service workloads and to AI agent workloads.

While the RISC {{RISC}} profile addresses risk signals for user accounts and the CAEP {{CAEP}} profile addresses continuous access evaluation for user sessions, no standardized event profile exists for communicating security-relevant state changes about workload identities. This specification fills that gap.

## Scope

WISE defines event types that enable:

- Trust domain authorities to signal credential and key lifecycle changes to federated peers and relying parties.
- Identity infrastructure components to communicate posture evaluation and policy changes that affect workload trust.
- Cross-domain signaling of trust material updates that require immediate action by relying parties.
- Runtime posture change notifications that may affect the trust evaluation of a workload.
- Supply-chain changes including updated or revoked provenance, and changes to the vulnerability status of a workload's components that require relying parties to re-evaluate trust.

## Alignment with WIMSE Architecture

This specification aligns with the WIMSE architecture {{WIMSE-ARCH}}, which defines a model where:

- A trust domain is a logical grouping of systems that share a common set of security controls and policies, identified by a fully qualified domain name.
- Workload identity credentials are issued under the authority of a trust domain, which maps to one or more trust anchors used to validate them.
- Workload identifiers are URIs that uniquely name a workload within a trust domain, as defined in {{WIMSE-ID}}.

Because a trust domain acts as the issuing authority for the workloads within it, WISE events are designed to signal state changes:

1. From a trust domain authority to federated peers, when changes affect the ability of external parties to validate or trust workloads from that domain.
2. From a trust domain authority to relying parties within the same domain, for credential and key lifecycle or posture changes that require action.

## Notational Conventions

{::boilerplate bcp14}

# Event Types

The base URI for WISE event types is:

~~~
https://schemas.openid.net/secevent/wise/event-type/
~~~

## Correlating Related Events {#correlating-related-events}

A single underlying occurrence may cause a Transmitter to emit more than one SET. For example, when a workload is disabled, the Transmitter emits a `workload-disabled` event together with an accompanying `credential-revoked` event for each credential the disablement invalidates.

A Transmitter SHOULD set the same value in the OPTIONAL `txn` (transaction identifier) claim {{RFC8417}} on all SETs arising from the same occurrence, whatever their event types, so that a Receiver can recognize that they share a cause.

## Common Mandatory Claims {#mandatory-optional-claims}

Every WISE event payload MUST include the following common mandatory claim. Mandatory common claims are omitted from the per-event attribute lists in the following sections to avoid repetition.

- **event_timestamp** - REQUIRED. A JSON number containing a NumericDate, as defined in {{RFC7519}}, that identifies when the event occurred. For an event that reports a decision or detection, it identifies when the decision was made or the condition was detected, respectively. `event_timestamp` is a member of the individual event payload, not a top-level SET claim. Its value can differ from the containing SET's top-level `iat` value and from the optional `effective_at` value.

## Common Optional Claims {#common-optional-claims}

Unless stated otherwise, any WISE event MAY include the common optional claims listed below. The `reason_admin`, `reason_user`, and `initiating_entity` claims are defined in Section 2 of {{CAEP}}. The `effective_at` claim is defined by this specification.

- **reason_admin** - OPTIONAL. A localizable administrative message intended for logging and auditing, as defined in {{CAEP}}. Its value is a JSON object containing one or more key/value pairs, where each key is a BCP 47 {{RFC5646}} language tag and each value is the locale-specific message.
- **reason_user** - OPTIONAL. A localizable, user-facing message, as defined in {{CAEP}}. Its value follows the same JSON object structure as `reason_admin`.
- **initiating_entity** - OPTIONAL. A JSON string describing what triggered the event, as defined in {{CAEP}}: one of `admin`, `user`, `policy`, or `system`.
- **effective_at** - OPTIONAL. A JSON number containing a NumericDate, as defined in {{RFC7519}}, that identifies when the change described by the event takes effect. `effective_at` is a member of the individual event payload, not a top-level SET claim. Its value MAY be earlier than, equal to, or later than the `iat` value of the containing SET. If `effective_at` is omitted, the change MUST be treated as effective as of the containing SET's `iat` value. A Receiver processing the SET at or after that time MUST apply the event-specific processing requirements immediately upon receipt. This claim does not override event-specific processing requirements.

To avoid repetition, these common claims are not listed in the per-event attribute definitions in the following sections; any event MAY carry them, and some examples include them for illustration.

When a WISE event includes `reason_admin` or `reason_user`, the claim MUST use the localizable JSON object structure defined above rather than a plain string. The following is a non-normative example:

~~~ json
"reason_admin": {
  "en": "Private key material detected in public repository",
  "de": "Privates Schluesselmaterial in oeffentlichem Repository entdeckt"
}
~~~

## Workload Credential Lifecycle Events

These events signal changes to the credentials issued to workloads by
the trust domain authority.

This specification defines event types that apply to workload
credentials regardless of their security properties. Inclusion of a
credential type in this specification does not constitute a
recommendation for its use. Deployments SHOULD prefer credentials with
proof-of-possession semantics as defined in {{WIMSE-CRED}}. Legacy
credential types are included to enable security event signaling for
environments operating heterogeneous credential ecosystems or
transitioning toward WIMSE-compliant infrastructure.

Proof-of-possession credentials bind a key to the workload identity: a WIT carries the public key in its `cnf` claim, and the corresponding private key is used to produce Workload Proof Tokens (WPT) {{WPT}}. WISE does not define separate events for the lifecycle of such keys. Because a bound key has no value once its credential is revoked, a compromised key is signaled by revoking the affected credential with `reason` set to `key_compromise` where applicable. An attempted credential rotation or renewal is signaled through `credential-rotated`, with `status` indicating whether the attempt succeeded or failed; where a replacement is issued outside a rotation, emit `credential-issued`.

### credential-issued

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/credential-issued`

The `credential-issued` event signals that a new credential was issued to a workload by the trust domain authority.

Attributes:

- **credential_type** - REQUIRED. The type of credential issued. Possible values:
    - `wit` - Workload Identity Token as defined in {{WIMSE-CRED}}
    - `wic` - Workload Identity Certificate as defined in {{WIMSE-CRED}}
    - `x509_svid` - X.509-SVID as defined in {{SPIFFE}}
    - `x509_generic` - Generic X.509 certificate not conforming to WIC or SVID profiles
    - `oauth_private_key_jwt` - OAuth 2.0 client authentication using private_key_jwt {{RFC7523}}
    - `oauth_mtls` - OAuth 2.0 mutual TLS client authentication {{RFC8705}}
    - `oauth_client_secret` - OAuth 2.0 client_id and client_secret credential pair
    - `api_key` - Static API key or long-lived bearer token
- Additional values MAY be defined by profiling specifications or private agreement between Transmitter and Receiver.
- **credential_id** - OPTIONAL. An identifier for the credential (e.g., certificate serial number, `jti` claim value).
- **expiry** - OPTIONAL. The expiration time of the credential as a JSON number (NumericDate per {{RFC7519}}).
- **key_storage** - OPTIONAL. Where the private key bound to the credential is stored. A Receiver MAY use this to inform its trust decision, for example whether to require additional assurance before relying on the credential. Possible values:
    - `software` - Key is held in software with no additional at-rest protection (filesystem, process memory, or an application-managed keystore).
    - `software_encrypted` - Key is held in software but encrypted at rest (for example, an OS keychain or an encrypted keystore) rather than in tamper-resistant hardware.
    - `hardware_tpm` - Key is protected by a Trusted Platform Module (TPM).
    - `hardware_hsm` - Key is protected by a Hardware Security Module (HSM).
    - `hardware_secure_enclave` - Key is protected by a secure enclave or an equivalent platform trusted-execution environment (for example, a mobile secure element or a TEE-backed keystore).
    - `vaulted` - Key is held by a secrets manager or vault and is never released to the workload in plaintext; the workload calls the vault to perform key operations.
    - `unknown` - The storage mechanism is not known to the Transmitter.
    - Additional values MAY be defined by profiling specifications or private agreement between Transmitter and Receiver.
- **key_storage_ecosystem** - OPTIONAL. Free-text description of the hardware or software environment protecting the key. Examples: "iPhone 17s, iOS 23 patch 6", "AWS Nitro Enclave", "Azure Confidential VM, AMD SEV-SNP", "FIPS 140-3 Level 3 HSM".

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-001",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/credential-issued": {
      "credential_type": "wic",
      "credential_id": "serial:ABC123DEF456",
      "expiry": 1700086400,
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-credential-issued title="Example: Credential Issued"}

### credential-rotated

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/credential-rotated`

The `credential-rotated` event reports the outcome of an attempt to rotate or renew a workload credential. The REQUIRED `status` claim indicates whether the attempt succeeded or failed. When `status` is `succeeded`, a new credential replaces the previous credential. When `status` is `failed`, no replacement was issued, and the current credential may remain usable until its natural expiry.

Attributes:

- **credential_type** - REQUIRED. The type of credential involved in the rotation attempt. Same values as in {{credential-issued}}.
- **status** - REQUIRED. A JSON string indicating the outcome of the rotation attempt. Possible values:
    - `succeeded` - A replacement credential was issued.
    - `failed` - The credential could not be rotated or renewed.
- **previous_credential_id** - OPTIONAL. Identifier of the credential that was replaced. This claim MAY be included only when `status` is `succeeded` and MUST be omitted when `status` is `failed`.
- **new_credential_id** - OPTIONAL. Identifier of the newly issued replacement credential. This claim MAY be included only when `status` is `succeeded` and MUST be omitted when `status` is `failed`.
- **grace_period_end** - OPTIONAL. The time until which the replaced credential remains valid, as a NumericDate. This claim MAY be included only when `status` is `succeeded` and MUST be omitted when `status` is `failed`.
- **credential_id** - OPTIONAL. Identifier of the current credential that could not be rotated or renewed. This claim MAY be included only when `status` is `failed` and MUST be omitted when `status` is `succeeded`.
- **current_expiry** - OPTIONAL. The natural expiration of the current credential, as a NumericDate. This claim MAY be included only when `status` is `failed` and MUST be omitted when `status` is `succeeded`.
- **failure_reason** - OPTIONAL. Why the rotation or renewal attempt failed. This claim MAY be included only when `status` is `failed` and MUST be omitted when `status` is `succeeded`. Possible values:
    - `credential_service_unreachable` - The Credential Service could not be reached (Section 3.2.1 of {{WIMSE-ARCH}}).
    - `posture_evaluation_failed` - The workload did not pass posture evaluation.
    - `policy_denied` - Issuance policy denied the rotation or renewal.
    - `internal_error` - An internal error occurred in the provisioning pipeline.

A failed rotation caused by posture evaluation is distinct from `posture-evaluated` with `result` set to `failed`, which reports the posture decision as a security signal in its own right. When posture evaluation causes the rotation to fail, a Transmitter emits `credential-rotated` with `status` set to `failed` and `failure_reason` set to `posture_evaluation_failed`, and MAY also emit `posture-evaluated` with `result` set to `failed`. When both events arise from the same occurrence, the Transmitter SHOULD set the same `txn` claim value on both SETs (see {{correlating-related-events}}).

The following examples are non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-002",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/credential-rotated": {
      "credential_type": "wit",
      "status": "succeeded",
      "previous_credential_id": "jti:wit-2024-q4-001",
      "new_credential_id": "jti:wit-2024-q4-002",
      "grace_period_end": 1700003600,
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-credential-rotated-succeeded title="Example: Credential Rotated (Succeeded)"}

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-005",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "txn": "wise-txn-005",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/credential-rotated": {
      "credential_type": "wit",
      "status": "failed",
      "credential_id": "jti:wit-2024-q4-001",
      "current_expiry": 1700003600,
      "failure_reason": "posture_evaluation_failed",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-credential-rotated-failed title="Example: Credential Rotated (Failed)"}

### credential-revoked

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/credential-revoked`

The `credential-revoked` event signals that a workload's credential was explicitly revoked before its natural expiry.

Attributes:

- **credential_type** - REQUIRED. The type of credential revoked.
- **credential_id** - OPTIONAL. Identifier of the revoked credential.
- **reason** - OPTIONAL. Why the credential was revoked. Possible values:
    - `compromise` - The credential is believed compromised.
    - `key_compromise` - The private key bound to the credential is believed compromised.
    - `superseded` - Replaced by a new credential.
    - `cessation` - The workload no longer operates.
    - `policy_violation` - Revoked due to a policy violation.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-003",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/credential-revoked": {
      "credential_type": "wic",
      "credential_id": "serial:ABC123DEF456",
      "reason": "compromise",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-credential-revoked title="Example: Credential Revoked"}

### credential-compromised

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/credential-compromised`

The `credential-compromised` event signals that a workload credential is believed to have been compromised. This is an advisory signal that may precede or accompany a `credential-revoked` event.

Attributes:

- **credential_type** - REQUIRED. The type of credential compromised.
- **credential_id** - OPTIONAL. Identifier of the compromised credential.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-004",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/credential-compromised": {
      "credential_type": "wit",
      "credential_id": "jti:wit-signing-key-2024-q4",
      "reason_admin": {
        "en": "Private key material detected in public repository"
      },
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-credential-compromised title="Example: Credential Compromised"}

## Workload Lifecycle Events

These events signal changes to the lifecycle state of a workload as managed by the trust domain authority.

These events convey workload lifecycle and graduated trust-level state changes. When a change has a corresponding credential effect, that effect is carried by a separate companion event (`credential-revoked` or `credential-issued`), correlated using the `txn` claim as described in {{correlating-related-events}}.

### workload-disabled

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/workload-disabled`

The `workload-disabled` event signals that the trust domain authority has suspended a workload. The authority will no longer issue credentials for this workload, and it cancels the workload's currently valid credentials. This event conveys only the lifecycle state change; the resulting credential cancellation is signaled separately through accompanying `credential-revoked` events. A Transmitter SHOULD emit those credential events together with this event.

Attributes:

- **reason** - OPTIONAL. Why the workload was disabled. Possible values:
    - `compromise` - The workload is believed compromised.
    - `policy_violation` - Suspended due to a policy violation.
    - `administrative` - Disabled by an administrator.
    - `maintenance` - Temporarily disabled for maintenance.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-010",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-disabled": {
      "reason": "compromise",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-workload-disabled title="Example: Workload Disabled"}

### workload-enabled

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/workload-enabled`

The `workload-enabled` event signals that a workload entered the active state. This includes a newly instantiated or started workload and a workload re-enabled after being disabled. A Receiver MUST NOT assume that the workload was previously disabled. The trust domain authority can begin or resume issuing credentials for the workload. This event conveys only the lifecycle state change; any credential provisioned as a result is signaled separately through an accompanying `credential-issued` event.

Attributes:


The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-011",
  "iat": 1700003600,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-enabled": {
      "event_timestamp": 1700003600
    }
  }
}
~~~
{: #fig-workload-enabled title="Example: Workload Enabled"}

### workload-trust-level-change

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/workload-trust-level-change`

The `workload-trust-level-change` event signals that the trust domain authority changed the graduated trust level of an operational workload without disabling or purging it. It covers movement to a lower trust level and movement to a higher trust level, including restoration to `full` trust.

This event is advisory. It conveys the authority's trust decision; each Receiver maps the reported level to concrete privilege changes according to its own policy. WISE does not prescribe the enforcement actions a Receiver takes.

Attributes:

- **trust_level** - REQUIRED. The workload's current trust level after the change, on a coarse graduated scale from highest to lowest:
    - `full` - No trust-based restriction is represented by this graduated signal.
    - `reduced` - Minor reduction; most privileges retained.
    - `restricted` - Significant reduction; only limited operations should be permitted.
    - `minimal` - Near-zero trust; only essential operations should be permitted, short of suspension.
- **previous_trust_level** - REQUIRED. The workload's trust level before the change. Possible values are `full`, `reduced`, `restricted`, and `minimal`, with the same meanings as for `trust_level`, plus:
    - `unknown` - The Transmitter cannot provide the previous trust level.

  The Transmitter MUST use `unknown` when it cannot provide the previous trust level. A Receiver that receives `unknown` MUST NOT infer a specific previous level or the direction of the change. When `previous_trust_level` is not `unknown`, it MUST differ from `trust_level`.
- **reason** - OPTIONAL. Why the workload's trust level changed. Possible values:
    - `anomaly_detected` - Anomalous behavior prompted a reduction in trust.
    - `policy_drift` - The workload drifted from its required policy or configuration.
    - `missing_provenance` - Required provenance was missing or could not be verified.
    - `posture_degraded` - Posture evaluation indicated a degraded but non-failing state.
    - `compliance_drift` - The workload drifted from a compliance requirement.
    - `remediated` - The condition that caused a lower trust level was remediated.
    - `posture_restored` - Posture evaluation returned to a satisfactory state.
    - `administrative` - The trust level was changed by an administrator.

  When the direction of the change is known, the Transmitter SHOULD choose a reason consistent with that direction. A Receiver MUST NOT infer the direction of a change solely from `reason` when `previous_trust_level` is `unknown`.

This event is complementary to related runtime and lifecycle events. `anomalous-behavior-detected` reports an observation, while `workload-trust-level-change` reports an authority decision that may follow such an observation. A `minimal` trust level keeps the workload operational and is not a substitute for `workload-disabled`. A `full` trust level does not re-enable a disabled workload or clear a prior `workload-compromised` signal.

The following examples are non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-013",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-trust-level-change": {
      "trust_level": "restricted",
      "previous_trust_level": "full",
      "reason": "anomaly_detected",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-workload-trust-level-change-degradation title="Example: Workload Trust Level Change (Degradation)"}

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-014",
  "iat": 1700003600,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-trust-level-change": {
      "trust_level": "full",
      "previous_trust_level": "restricted",
      "reason": "remediated",
      "event_timestamp": 1700003600
    }
  }
}
~~~
{: #fig-workload-trust-level-change-restoration title="Example: Workload Trust Level Change (Restoration)"}

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-015",
  "iat": 1700007200,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-trust-level-change": {
      "trust_level": "reduced",
      "previous_trust_level": "unknown",
      "event_timestamp": 1700007200
    }
  }
}
~~~
{: #fig-workload-trust-level-change-unknown title="Example: Workload Trust Level Change (Unknown Previous Level)"}

### workload-purged

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/workload-purged`

The `workload-purged` event signals that a workload has been permanently removed from the trust domain. This is irreversible. The workload will not be re-provisioned. All credentials previously issued for this workload MUST be considered invalid. As with `workload-disabled`, this event conveys only the lifecycle state change; the resulting credential cancellation is signaled separately through accompanying `credential-revoked` events.

Attributes:


The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-012",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-purged": {
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-workload-purged title="Example: Workload Purged"}

## Trust and Policy Events

These events signal changes to trust material, federation relationships, and policies governing workload identity issuance, posture evaluation, and credential validation.

Two related lifecycles are covered, each modeled with explicit create, update, and delete events. The trust anchors (keys and CAs) that validate a trust domain's credentials are managed through `trust-anchor-added`, `trust-anchor-rotated`, and `trust-anchor-revoked`. The federation relationship between trust domains, which determines whether one domain accepts credentials issued by another, is managed through `trust-domain-federation-established`, `trust-domain-federation-updated`, and `trust-domain-federation-revoked`. Replacing an entire trust bundle in a single operation is conveyed as the corresponding set of `trust-anchor-added` and `trust-anchor-revoked` events.

### trust-anchor-added

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/trust-anchor-added`

The `trust-anchor-added` event signals that a new trust anchor was added for a trust domain, alongside any existing anchors. Examples include pre-staging a new signing key ahead of a rotation, or bringing online a new data center, region, or cloud provider that operates under the same trust domain. Relying parties and federated peers MUST add the new material to the set they accept for the domain.

Attributes:

- **anchor_type** - REQUIRED. The type of trust material. Possible values:
    - `x509_ca` - X.509 CA certificate(s) used to validate Workload Identity Certificates (WIC) or X.509-SVIDs.
    - `jwks` - JSON Web Key Set {{RFC7517}} used to validate Workload Identity Tokens (WIT).
- **trust_domain** - REQUIRED. The FQDN of the trust domain the anchor belongs to.
- **key_id** - OPTIONAL. Identifier of the added anchor. For JWKS, the `kid` value. For X.509, the certificate serial number or Subject Key Identifier.
- **key_details** - OPTIONAL. A JSON object carrying selected JSON Web Key (JWK) parameters that describe the added key, so a Receiver can act (for example, recognize support for a new signature algorithm such as ECDSA alongside RSA) without fetching and comparing the bundle. The object is not itself a complete JWK. When present, `key_details` MUST contain `kty` and MAY contain `alg`, `use`, and `crv`. These members use the names and semantics of the corresponding JWK parameters defined in {{RFC7517}} and {{RFC7518}}, and their values SHOULD be taken from the applicable JSON Object Signing and Encryption (JOSE) registries {{IANA.JOSE}}:
    - `kty` - REQUIRED. The JWK Key Type value identifying the cryptographic algorithm family used with the key (e.g., `EC`, `RSA`, `OKP`, or `oct`).
    - `alg` - OPTIONAL. The JWK Algorithm value identifying the algorithm intended for use with the key (e.g., `RS256`, `ES256`, or `EdDSA`).
    - `use` - OPTIONAL. The JWK Public Key Use value identifying the intended use of the public key (e.g., `sig` or `enc`).
    - `crv` - OPTIONAL. The JWK Curve value identifying the cryptographic curve used with an EC or OKP key (e.g., `P-256` or `Ed25519`). This member is applicable only to key types for which a `crv` parameter is defined.
- **jwks_uri** - OPTIONAL. When `anchor_type` is `jwks`, the URI to fetch the updated JWK Set.
- **x509_bundle_uri** - OPTIONAL. When `anchor_type` is `x509_ca`, the URI to fetch the updated CA bundle.
- **reason** - OPTIONAL. Why the anchor was added. Possible values:
    - `new_environment` - A new environment (data center, region, or cloud provider) under the same trust domain was brought online.
    - `additional_key` - An additional concurrent anchor was introduced (for example, to support a new signature algorithm alongside an existing one).
    - `scheduled_rotation` - A new key was pre-staged ahead of a scheduled rotation.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-020",
  "iat": 1700000000,
  "aud": "https://federation-peer.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/trust-anchor-added": {
      "anchor_type": "jwks",
      "trust_domain": "trust.example.com",
      "jwks_uri": "https://authority.example.com/.well-known/jwks.json",
      "key_id": "kid:ecdsa-2026",
      "key_details": {
        "kty": "EC",
        "alg": "ES256",
        "use": "sig",
        "crv": "P-256"
      },
      "effective_at": 1700000000,
      "reason": "additional_key",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-trust-anchor-added title="Example: Trust Anchor Added"}

### trust-anchor-rotated

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/trust-anchor-rotated`

The `trust-anchor-rotated` event signals that an existing trust anchor for a trust domain was replaced by new material. Relying parties and federated peers MUST begin accepting the new anchor and SHOULD stop accepting the previous one once any grace period ends.

Attributes:

- **anchor_type** - REQUIRED. The type of trust material. Same values as in {{trust-anchor-added}}.
- **trust_domain** - REQUIRED. The FQDN of the trust domain whose anchor was rotated.
- **previous_key_id** - OPTIONAL. Identifier of the anchor being replaced.
- **new_key_id** - OPTIONAL. Identifier of the replacement anchor.
- **jwks_uri** - OPTIONAL. When `anchor_type` is `jwks`, the URI to fetch the updated JWK Set.
- **x509_bundle_uri** - OPTIONAL. When `anchor_type` is `x509_ca`, the URI to fetch the updated CA bundle.
- **old_material_expiry** - OPTIONAL. When the previous material ceases to be valid (grace period end). JSON number (NumericDate).
- **reason** - OPTIONAL. Why the rotation occurred. Possible values:
    - `scheduled_rotation` - Routine key rotation.
    - `compromise` - The previous anchor is believed compromised.
    - `policy_change` - Rotated due to updated security policy.
    - `expiry` - Proactive rotation before scheduled expiry.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-021",
  "iat": 1700000000,
  "aud": "https://federation-peer.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/trust-anchor-rotated": {
      "anchor_type": "jwks",
      "trust_domain": "trust.example.com",
      "previous_key_id": "kid:signing-2024-q4",
      "new_key_id": "kid:signing-2025-q1",
      "jwks_uri": "https://authority.example.com/.well-known/jwks.json",
      "effective_at": 1700000000,
      "old_material_expiry": 1700604800,
      "reason": "scheduled_rotation",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-trust-anchor-rotated title="Example: Trust Anchor Rotated (JWKS Rotation)"}

### trust-anchor-revoked

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/trust-anchor-revoked`

The `trust-anchor-revoked` event signals that a trust anchor for a trust domain was withdrawn and MUST no longer be used to validate credentials. This covers both explicit revocation (for example, a compromised CA) and removal of an anchor that has reached end of life.

Attributes:

- **anchor_type** - REQUIRED. The type of trust material. Same values as in {{trust-anchor-added}}.
- **trust_domain** - REQUIRED. The FQDN of the trust domain whose anchor was revoked.
- **key_id** - OPTIONAL. Identifier of the revoked anchor. For JWKS, the `kid` value. For X.509, the certificate serial number or Subject Key Identifier.
- **jwks_uri** - OPTIONAL. When `anchor_type` is `jwks`, the URI to fetch the JWK Set reflecting the removal.
- **x509_bundle_uri** - OPTIONAL. When `anchor_type` is `x509_ca`, the URI to fetch the CA bundle reflecting the removal.
- **reason** - OPTIONAL. Why the anchor was revoked. Possible values:
    - `compromise` - The anchor is believed compromised.
    - `policy_change` - Revoked due to updated security policy.
    - `superseded` - Replaced by other material and no longer needed.
    - `expiry` - The anchor reached end of life.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-022",
  "iat": 1700000000,
  "aud": "https://federation-peer.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/trust-anchor-revoked": {
      "anchor_type": "x509_ca",
      "trust_domain": "trust.example.com",
      "key_id": "serial:CA-ROOT-2023-001",
      "reason": "compromise",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-trust-anchor-revoked title="Example: Trust Anchor Revoked (CA Compromise)"}

### trust-domain-federation-established

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/trust-domain-federation-established`

The `trust-domain-federation-established` event signals that a new trust domain has been federated. Relying parties and federated peers MAY begin accepting workload credentials originating from the specified trust domain, validated against the associated trust anchors. This is the counterpart to `trust-domain-federation-revoked`, and typically accompanies the trust material for the new domain (either inline via `jwks_uri` / `x509_bundle_uri` or through subsequent `trust-anchor-added` events).

Attributes:

- **trust_domain** - REQUIRED. The FQDN of the newly federated trust domain.
- **anchor_type** - OPTIONAL. The type of trust material used to validate credentials from the new domain. Same values as in {{trust-anchor-added}}: `x509_ca` or `jwks`.
- **jwks_uri** - OPTIONAL. When `anchor_type` is `jwks`, the URI to fetch the JWK Set {{RFC7517}} for the federated domain.
- **x509_bundle_uri** - OPTIONAL. When `anchor_type` is `x509_ca`, the URI to fetch the CA bundle for the federated domain.
- **reason** - OPTIONAL. Why federation was established. Possible values:
    - `onboarding` - A new organization joined the federation.
    - `expansion` - A new data center, region, or cloud provider was added to the enterprise.
    - `administrative` - Administrative decision to establish federation.
    - `contractual` - A new business relationship was established.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-023",
  "iat": 1700000000,
  "aud": "https://federation-peer.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://newpartner.example.org"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/trust-domain-federation-established": {
      "trust_domain": "newpartner.example.org",
      "anchor_type": "jwks",
      "jwks_uri": "https://authority.newpartner.example.org/.well-known/jwks.json",
      "effective_at": 1700000000,
      "reason": "onboarding",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-federation-established title="Example: Trust Domain Federation Established"}

### trust-domain-federation-updated

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/trust-domain-federation-updated`

The `trust-domain-federation-updated` event signals that the terms of an existing federation with a trust domain changed without revoking it. Examples include a change to the set of workloads or name constraints that are trusted, or a change to which trust anchors validate the federated domain. Relying parties MUST update their federation configuration accordingly while continuing to trust the domain.

Attributes:

- **trust_domain** - REQUIRED. The FQDN of the federated trust domain whose federation terms changed.
- **reason** - OPTIONAL. Why the federation was updated. Possible values:
    - `policy_change` - Updated due to a change in federation policy.
    - `anchor_update` - The trust anchors used to validate the federated domain changed.
    - `administrative` - Administrative decision.
    - `contractual` - Business relationship terms changed.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-024",
  "iat": 1700000000,
  "aud": "https://federation-peer.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://newpartner.example.org"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/trust-domain-federation-updated": {
      "trust_domain": "newpartner.example.org",
      "reason": "anchor_update",
      "effective_at": 1700000000,
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-federation-updated title="Example: Trust Domain Federation Updated"}

### trust-domain-federation-revoked

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/trust-domain-federation-revoked`

The `trust-domain-federation-revoked` event signals that a previously federated trust domain is no longer trusted. All workload credentials originating from the specified trust domain MUST be rejected.

Attributes:

- **trust_domain** - REQUIRED. The FQDN of the trust domain that is no longer trusted.
- **reason** - OPTIONAL. Why federation was revoked. Possible values:
    - `compromise` - The federated domain is believed compromised.
    - `policy_violation` - Federation revoked due to policy.
    - `administrative` - Administrative decision to end federation.
    - `contractual` - Business relationship ended.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-025",
  "iat": 1700000000,
  "aud": "https://federation-peer.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://partner.example.org"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/trust-domain-federation-revoked": {
      "trust_domain": "partner.example.org",
      "reason": "administrative",
      "effective_at": 1700604800,
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-federation-revoked title="Example: Trust Domain Federation Revoked"}

### policy-changed

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/policy-changed`

The `policy-changed` event signals that a policy governing workload identity issuance, posture evaluation, or credential validation has changed. The REQUIRED `policy_type` claim identifies the category of policy that changed.

When `policy_type` is `issuance`, the change can affect workload eligibility, credential types, lifetime constraints, or required posture evidence. When `policy_type` is `posture_evaluation`, the change can affect required evidence, evaluation criteria, or deployment-context signals during credential issuance and renewal. When `policy_type` is `validation`, the change can affect trust-anchor validation, claim validation, audience restrictions, or acceptable credential types.

Attributes:

- **policy_type** - REQUIRED. A JSON string identifying the category of policy that changed. Possible values:
    - `issuance` - The policy governing credential issuance for workloads.
    - `posture_evaluation` - The policy governing posture evaluation during credential issuance or renewal.
    - `validation` - The policy used by relying parties to validate workload identity credentials.
- **policy_id** - OPTIONAL. Identifier of the policy that changed.

The following examples are non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-050",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/policy-changed": {
      "policy_type": "issuance",
      "policy_id": "policy:issuance-v3",
      "effective_at": 1700000000,
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-policy-changed-issuance title="Example: Policy Changed (Issuance)"}

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-051",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/policy-changed": {
      "policy_type": "posture_evaluation",
      "policy_id": "policy:posture-eval-v2",
      "effective_at": 1700000000,
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-policy-changed-posture-evaluation title="Example: Policy Changed (Posture Evaluation)"}

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-052",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/policy-changed": {
      "policy_type": "validation",
      "policy_id": "policy:validation-v5",
      "effective_at": 1700000000,
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-policy-changed-validation title="Example: Policy Changed (Validation)"}

## Posture and Runtime Events

### posture-evaluated

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/posture-evaluated`

The `posture-evaluated` event signals that a workload posture evaluation completed. The REQUIRED `result` claim indicates whether the workload passed or failed the evaluation. The Credential Service evaluates the workload's runtime environment, software integrity, or deployment context against the applicable requirements.

Posture evaluation may occur during initial issuance, renewal, or continuous re-evaluation, and this event reports the outcome independently of any particular credential operation. A `failed` result is a security-critical signal in its own right and does not necessarily cause credential rotation to fail; conversely, `credential-rotated` with `status` set to `failed` may occur for reasons unrelated to posture. When posture evaluation causes a rotation or renewal attempt to fail, the events are emitted and correlated as described in {{credential-rotated}}.

A `succeeded` result does not imply that a prior failure occurred. It confirms that the workload met the evaluated requirements but does not by itself assert that a credential was issued. A related `credential-rotated` event with `status` set to `succeeded` reports successful replacement issuance.

Attributes:

- **result** - REQUIRED. A JSON string indicating the outcome of the posture evaluation. Possible values:
    - `failed` - The workload did not pass posture evaluation.
    - `succeeded` - The workload passed posture evaluation.
- **evaluation_type** - OPTIONAL. The scope of the evaluation. Possible values:
    - `platform` - Platform-level evaluation (e.g., node integrity, TEE verification).
    - `workload` - Workload-level evaluation (e.g., binary identity, image hash).
    - `runtime` - Runtime environment evaluation (e.g., configuration compliance, network posture).
- **reason** - OPTIONAL. Why the evaluation failed. This claim MAY be included only when `result` is `failed` and MUST be omitted when `result` is `succeeded`. Possible values:
    - `platform_integrity_failed` - Platform integrity or TEE verification did not pass.
    - `attestation_invalid` - Attestation evidence was missing, malformed, or could not be verified.
    - `image_mismatch` - The workload's binary or image did not match the expected measurement.
    - `configuration_noncompliant` - The runtime configuration did not meet policy.
    - `policy_denied` - Posture evaluation policy denied the workload.

The following examples are non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-053",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "txn": "wise-txn-005",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/posture-evaluated": {
      "result": "failed",
      "evaluation_type": "platform",
      "reason": "platform_integrity_failed",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-posture-evaluated-failed title="Example: Posture Evaluated (Failed)"}

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-054",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/posture-evaluated": {
      "result": "succeeded",
      "evaluation_type": "workload",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-posture-evaluated-succeeded title="Example: Posture Evaluated (Succeeded)"}

### workload-execution-environment-changed

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/workload-execution-environment-changed`

The `workload-execution-environment-changed` event signals that a workload's runtime environment or deployment context has changed. This may trigger re-evaluation of trust by relying parties or require the workload to undergo posture evaluation before new credentials are issued.

Attributes:

- **reason** - OPTIONAL. Why the workload execution environment changed. Possible values:
    - `migration` - Workload moved to a different node, region, or zone.
    - `scaling` - New instances added or removed.
    - `redeployment` - Workload was redeployed (same identity, new instance).
    - `node_reassignment` - Underlying compute node changed.
- **previous_context** - OPTIONAL. A JSON object describing the prior runtime environment. See the guidance below on the interoperable key set and on limiting sensitive detail.
- **current_context** - OPTIONAL. A JSON object describing the new runtime environment, using the same keys as `previous_context` so the two can be compared.
- **posture_evaluation_status** - OPTIONAL. Whether posture re-evaluation has occurred. Possible values:
    - `succeeded` - Re-evaluation completed successfully.
    - `pending` - Re-evaluation has not yet occurred.
    - `failed` - Re-evaluation was attempted and failed.

For interoperability, when `previous_context` or `current_context` is present it MAY include the following keys. Each is OPTIONAL, because the corresponding notion may not exist in every deployment:

- `region` - The geographic or cloud region.
- `zone` - The availability or fault-isolation zone.
- `platform` - The runtime platform type (for example, `kubernetes`, `ecs`, `vm`, or `serverless`).
- `cluster` - An identifier for the orchestration cluster or scheduling domain.
- `image` - A reference or digest for the workload image or binary.

This set is the minimum interoperable capability: where a Receiver understands these keys, it can compare the prior and new environment without prior agreement. Both objects SHOULD use the same keys so they can be compared. The schema MAY be extended with additional deployment- or profile-specific topology information where a Transmitter sees fit; in that case the Transmitter is responsible for ensuring the Receiver can understand the added fields, and how such understanding is established is out of scope for this profile.

These fields can reveal workload infrastructure topology; see {{privacy-considerations}} for guidance on limiting the detail disclosed.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-030",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-execution-environment-changed": {
      "reason": "migration",
      "previous_context": {
        "region": "us-east-1",
        "platform": "kubernetes"
      },
      "current_context": {
        "region": "eu-west-1",
        "platform": "kubernetes"
      },
      "posture_evaluation_status": "succeeded",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-execution-environment-changed title="Example: Workload Execution Environment Changed (Migration)"}

### workload-compromised

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/workload-compromised`

The `workload-compromised` event signals that a workload is believed to be compromised based on runtime detection. This is a high-severity signal that SHOULD trigger immediate isolation or credential revocation. Where the Transmitter is also the credential authority for the workload, it SHOULD emit an accompanying `credential-revoked` event (with reason `compromise`), correlated using the `txn` claim as described in {{correlating-related-events}}.

Attributes:

- **detection_method** - OPTIONAL. How the compromise was detected.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-060",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-compromised": {
      "detection_method": "runtime-anomaly-detection",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-workload-compromised title="Example: Workload Compromised"}

### anomalous-behavior-detected

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/anomalous-behavior-detected`

The `anomalous-behavior-detected` event signals that unusual behavior was observed for a workload. This is an advisory signal of lower severity than `workload-compromised`, intended for SIEM and SOC integration.

Attributes:

- **anomaly_type** - OPTIONAL. Category of the anomaly.
- **severity** - OPTIONAL. Severity level. Possible values:
    - `low`
    - `medium`
    - `high`
    - `critical`

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-061",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/anomalous-behavior-detected": {
      "anomaly_type": "unexpected-egress",
      "severity": "medium",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-anomalous-behavior-detected title="Example: Anomalous Behavior Detected"}

## Supply Chain Events

These events signal changes in a workload's supply chain including the provenance of the software it is built from and the vulnerability status of its components. The underlying detail such as a Software Bill of Materials (SBOM), a build attestation, or a vulnerability advisory is typically held in a separate document maintained by other tooling. These events act as signals that inform a relying party that something relevant has changed, and where to obtain the detail, rather than carrying the full supply-chain record inline.

### workload-provenance-changed

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/workload-provenance-changed`

The `workload-provenance-changed` event signals that the provenance of a workload, such as its SBOM or a build attestation has changed. This includes newly available or updated provenance, and the revocation or failed verification of previously trusted provenance. A relying party may re-evaluate its trust in the workload, or fetch the referenced document to assess the change.

Attributes:

- **change_type** - REQUIRED. The nature of the change. Possible values:
    - `updated` - New or updated provenance (for example, a new SBOM) is available.
    - `revoked` - Previously trusted provenance or an attestation is no longer valid.
    - `verification_failed` - Verification of the workload's provenance failed.
- **provenance_uri** - OPTIONAL. A URI at which the affected provenance document can be retrieved.
- **provenance_format** - OPTIONAL. A hint indicating the kind of document referenced, for example `sbom` or `attestation`.
- **artifact_digest** - OPTIONAL. A digest of the workload artifact (such as a container image) that the provenance describes, allowing the relying party to correlate the event with what is running.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-040",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-provenance-changed": {
      "change_type": "revoked",
      "provenance_uri": "https://provenance.example.com/payment-service/attestation",
      "reason_admin": {
        "en": "Build provenance attestation revoked by source repository owner"
      },
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-provenance-changed title="Example: Workload Provenance Changed"}

### workload-vulnerability-status-changed

Event Type URI: `https://schemas.openid.net/secevent/wise/event-type/workload-vulnerability-status-changed`

The `workload-vulnerability-status-changed` event signals a change in the status of a vulnerability with respect to a workload. The status values follow the Vulnerability Exploitability eXchange (VEX) model {{VEX}}, which distinguishes whether a workload is actually affected by a known vulnerability. This allows a transmitter both to warn a relying party that a workload has become affected, and to relax a prior warning when a vulnerability is found not to apply or has been fixed.

Attributes:

- **vulnerability_id** - REQUIRED. A public identifier for the vulnerability, such as a CVE identifier.
- **status** - REQUIRED. The status of the workload with respect to the vulnerability, following the VEX model {{VEX}}. Possible values:
    - `affected` - Actions are recommended to remediate or address the vulnerability.
    - `not_affected` - No remediation is required (for example, the vulnerable code is not reachable).
    - `fixed` - This workload contains a fix for the vulnerability.
    - `under_investigation` - Whether the workload is affected is not yet known.
- **severity** - OPTIONAL. A qualitative severity to help the relying party prioritize. Possible values: `low`, `medium`, `high`, `critical`.
- **advisory_uri** - OPTIONAL. A URI at which a full advisory or VEX statement can be retrieved.

The following example is non-normative.

~~~ json
{
  "iss": "https://authority.example.com/",
  "jti": "wise-evt-041",
  "iat": 1700000000,
  "aud": "https://rp.partner.example.net/wise",
  "sub_id": {
    "format": "uri",
    "uri": "wimse://trust.example.com/workload/payment-service"
  },
  "events": {
    "https://schemas.openid.net/secevent/wise/event-type/workload-vulnerability-status-changed": {
      "vulnerability_id": "CVE-2026-12345",
      "status": "affected",
      "severity": "critical",
      "advisory_uri": "https://advisories.example.com/CVE-2026-12345",
      "event_timestamp": 1700000000
    }
  }
}
~~~
{: #fig-vulnerability-status-changed title="Example: Workload Vulnerability Status Changed"}

# Subject Identifiers for Workload Events

Following the Shared Signals Framework {{SSF}}, every WISE event conveys its subject in a top-level `sub_id` claim — a sibling of `iss`, `jti`, `iat`, `aud`, and `events`, not a member of the event-specific payload. The value of `sub_id` is a Subject Identifier as defined in {{RFC9493}}. WISE uses the `uri` format, carrying a Workload Identifier expressed as a URI following {{WIMSE-ID}}.

## URI Format

The `uri` format is the primary subject identifier format for WISE events. It carries the Workload Identifier as defined in {{WIMSE-ID}}, which is a URI containing a trust domain in the authority component and a workload-specific path.

The following example is non-normative.

~~~ json
{
  "format": "uri",
  "uri": "wimse://trust.example.com/workload/payment-service"
}
~~~

Deployments using SPIFFE identifiers {{SPIFFE}} express the subject using the `spiffe` URI scheme:

The following example is non-normative.

~~~ json
{
  "format": "uri",
  "uri": "spiffe://trust.example.com/ns/production/sa/payment-service"
}
~~~

Deployments using OAuth 2.0 Client ID Metadata Documents {{CIMD}} may express the workload subject using the client identifier URI:

The following example is non-normative.

~~~ json
{
  "format": "uri",
  "uri": "https://client.example.com/.well-known/oauth-client"
}
~~~

Deployments identifying workloads primarily by their immutable cryptographic artifacts, such as a compiled binary hash or container image digest, MAY express the workload subject using the Named Information (`ni`) URI scheme defined in {{RFC6920}}. This lets relying parties cryptographically bind security events directly to a workload's build provenance or artifact registry digest, and is particularly useful for supply-chain events and for external monitoring or intelligence platforms that key off artifact hashes rather than the full trust-domain context.

A digest identifies an artifact — and therefore potentially every instance built from it — rather than a single running workload or its Workload Identifier. The `ni` form is therefore best suited to artifact-scoped signals and as a correlation aid; the Workload Identifier `uri` form remains the primary subject for per-workload and credential events.

The following example is non-normative.

~~~ json
{
  "format": "uri",
  "uri": "ni://trust.example.com/sha-256;f4OxZX_x_FO5LcGBSKHWXfwtSx-j1ncoSt3SABJtkGk"
}
~~~

## Trust Domain Subject

For events that apply to an entire trust domain rather than to a single workload — the `trust-anchor-*` and `trust-domain-federation-*` events, and the `policy-changed` event — the `sub_id` identifies the trust domain itself using its Workload Identifier Origin as defined in {{WIMSE-ID}}:

The following example is non-normative.

~~~ json
{
  "format": "uri",
  "uri": "wimse://trust.example.com"
}
~~~

# Security Considerations

## Confidentiality

WISE events MAY contain sensitive information about workload infrastructure topology, credential identifiers, and security posture. All network requests in this protocol MUST use TLS, and the use of TLS MUST follow the recommendations in {{RFC9325}}. Events SHOULD be encrypted using JSON Web Encryption (JWE) {{RFC7516}} when transmitted across trust domain boundaries.

## Replay and Freshness

Receivers MUST validate the `iat` claim and reject events that are unreasonably old. Receivers SHOULD maintain a record of recently received `jti` values to detect replay attacks.

## Authorization

Access to WISE event streams MUST be authorized. Transmitters MUST verify that Receivers are authorized to receive events for the specified subjects. A trust domain authority SHOULD NOT transmit workload-level events to parties that have no trust relationship with those workloads.

## Compromise Response

Several WISE events indicate that a credential, key, trust anchor, federation, or workload can no longer be trusted. This is signaled either by an event whose purpose is to report compromise (`credential-compromised`, `workload-compromised`) or by any event carrying a `reason` of `compromise` or `key_compromise`. Upon receiving any such event — regardless of event type, including events defined by future revisions or profiling specifications — Receivers SHOULD take immediate action appropriate to the affected object (for example, reject the affected credentials, keys, or trust material, or isolate the affected workload) without waiting for additional confirmation.

Events in this document that carry such a signal include `credential-compromised`; `credential-revoked` (with reason `compromise` or `key_compromise`); `trust-anchor-rotated` and `trust-anchor-revoked` (with reason `compromise`); `trust-domain-federation-revoked` (with reason `compromise`); `workload-disabled` (with reason `compromise`); and `workload-compromised`.

Rejecting credentials only takes effect at the next credential check or proof-of-possession step; neither short credential lifetime nor condition-liveness severs a connection that is already established. A Receiver that holds active connections with, or is actively serving, the affected workload SHOULD therefore also terminate those connections rather than waiting for the next operation. This is best-effort and applies to Receivers, such as gateways or service mesh components, that are able to correlate the workload identifier to live connections.

## Relationship to Credential Freshness Models

Deployments use different mechanisms to limit the exposure window of a compromised or deprovisioned workload:

- Issuer-side status signaling, where the trust domain authority communicates lifecycle changes to relying parties through an event channel. The events defined in this specification serve this purpose.
- Short credential lifetime, where the remaining validity period bounds the exposure window. In the WIMSE model, credentials are intentionally short-lived to force posture evaluation before re-issuance.
- Condition-liveness, where a locally observable condition (hardware release policy, TEE state, platform integrity measurement) gates each key operation. Failure of the condition prevents the next presentation or handshake step without requiring a remote signal.

These mechanisms are complementary, not mutually exclusive. Condition-bounded credentials reduce the local deprovisioning window but cannot observe externally originated changes: issuer policy withdrawal, trust anchor rotation, cross-domain incident response, or administrative decisions to terminate an established connection. WISE events address these cases. Deployments combining short-lived credentials with condition-liveness properties still benefit from issuer-side signaling for lifecycle changes that no local mechanism can detect.

## Supply Chain Signals

Supply chain events are advisory inputs to a Receiver's own decision-making. A Receiver SHOULD treat a `workload-vulnerability-status-changed` event as information to be evaluated against its own policies, rather than as a directive to be enforced automatically. In particular, a Receiver SHOULD NOT block, revoke, or otherwise restrict a workload's access solely because such an event was received. It SHOULD weigh the event together with the referenced advisory, the reported status and severity, and its own risk posture before deciding what action, if any, to take. A `revoked` provenance change, once validated, indicates that the affected provenance MUST NOT be relied upon.

# Privacy Considerations

WISE events may reveal information about internal infrastructure, deployment patterns, scaling behavior, and security incidents. Transmitters SHOULD minimize the information disclosed to what is necessary for the Receiver to take appropriate action.

Several fields in this specification carry free-form or descriptive values — for example `key_storage_ecosystem` on `credential-issued`, and `previous_context` / `current_context` on `workload-execution-environment-changed`. Such fields can inadvertently disclose fine-grained infrastructure or device detail, such as node or host names, IP addresses, internal network identifiers, device models, or software versions. For any such field, a Transmitter SHOULD avoid including detail beyond what the Receiver needs, based on its own evaluation of the privacy risk, particularly for events that may cross a trust-domain boundary. For example, a coarse `region` is preferable to a specific node or host name.

Events SHOULD NOT include personally identifiable information. Workload identifiers SHOULD NOT encode information about the humans who manage or operate the workloads.

# IANA Considerations

## WISE Event Types Registry

This document requests IANA to establish a new registry titled "WISE Event Types". This registry records the Security Event Token (SET) event types defined by the WISE profile and by future extensions, so that such extensions are discoverable and their identifiers do not collide.

The full Event Type URI of a registered entry is formed by appending its registered Event Type value to the WISE event type base URI:

~~~
https://schemas.openid.net/secevent/wise/event-type/
~~~

### Registration Procedure

Registration follows the Specification Required policy defined in Section 4.6 of {{RFC8126}}. The Designated Expert verifies that a registration references a permanent, publicly available specification, that the Event Type value is unique within the registry and consists of lowercase ASCII letters, digits, and hyphens, and that its meaning does not overlap an existing entry.

### Registry Fields

Each entry has the following fields:

- **Event Type**: The path segment appended to the base URI to form the full Event Type URI.
- **Description**: A brief description of the event type.
- **Change Controller**: For entries in this document, the OpenID Foundation. For other entries, the party responsible for the registration.
- **Reference**: The specification defining the event type.

### Initial Registry Contents

The Change Controller for every entry below is the OpenID Foundation, and the Reference for every entry is this document. IANA is requested to register the following Event Type values:

Event Type:
: `credential-issued`

Description:
: A new credential was issued to a workload.

Event Type:
: `credential-rotated`

Description:
: A workload credential rotation completed, indicating whether it succeeded or failed.

Event Type:
: `credential-revoked`

Description:
: A workload's credential was revoked before its natural expiry.

Event Type:
: `credential-compromised`

Description:
: A workload credential is believed to be compromised.

Event Type:
: `workload-disabled`

Description:
: A workload was suspended by the trust domain authority.

Event Type:
: `workload-enabled`

Description:
: A workload entered the active state, either initially or after being re-enabled.

Event Type:
: `workload-purged`

Description:
: A workload was permanently removed from the trust domain.

Event Type:
: `workload-trust-level-change`

Description:
: A workload's graduated trust level changed without suspending it.

Event Type:
: `trust-anchor-added`

Description:
: A new trust anchor was added for a trust domain.

Event Type:
: `trust-anchor-rotated`

Description:
: An existing trust anchor was replaced.

Event Type:
: `trust-anchor-revoked`

Description:
: A trust anchor was withdrawn and must no longer be used.

Event Type:
: `trust-domain-federation-established`

Description:
: A new trust domain was federated.

Event Type:
: `trust-domain-federation-updated`

Description:
: The terms of an existing federation changed.

Event Type:
: `trust-domain-federation-revoked`

Description:
: A previously federated trust domain is no longer trusted.

Event Type:
: `policy-changed`

Description:
: A workload identity issuance, posture evaluation, or credential validation policy changed.

Event Type:
: `posture-evaluated`

Description:
: A workload posture evaluation completed, indicating whether the workload passed or failed.

Event Type:
: `workload-execution-environment-changed`

Description:
: A workload's runtime environment or deployment context changed.

Event Type:
: `workload-compromised`

Description:
: A workload is believed to be compromised based on runtime detection.

Event Type:
: `anomalous-behavior-detected`

Description:
: Unusual behavior was observed for a workload.

Event Type:
: `workload-provenance-changed`

Description:
: The provenance of a workload changed.

Event Type:
: `workload-vulnerability-status-changed`

Description:
: The vulnerability status of a workload changed.

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to thank the members of the OpenID Foundation Shared Signals Working Group and the IETF WIMSE Working Group for their contributions to this specification.

# Reviewers and Contributors
{:numbered="false"}

The authors want to recognize the contributions and reviews of the following individuals (in alphabetical order):

- Apoorva Deshpande
- Matt Topper
- Tom Sato

# Document History
{:numbered="false"}

-04

- Broadened `workload-enabled` to represent a workload entering the active state either when newly instantiated or started, or when re-enabled after being disabled; Receivers must not assume a prior disabled state.
- Folded `credential-renewal-failure` into `credential-rotated`, which now carries a REQUIRED `status` of `succeeded` or `failed`; retained success-only replacement fields and failure-only `credential_id`, `current_expiry`, and `failure_reason`, and removed the separate IANA registration.
- Replaced `workload-degraded` and `workload-restored` with a single `workload-trust-level-change` event carrying REQUIRED `trust_level` and `previous_trust_level` claims; added `full` to the current-level scale and `unknown` for an unavailable previous level, retained advisory semantics and direction-appropriate reasons, and replaced the two IANA registrations with one.
- Replaced `issuance-policy-changed`, `posture-evaluation-policy-changed`, and `validation-policy-changed` with a single `policy-changed` event carrying a REQUIRED `policy_type` of `issuance`, `posture_evaluation`, or `validation`; retained the OPTIONAL `policy_id`, common timing claims, trust-domain subject, examples for all three policy categories, and matching IANA registration.
- Replaced `posture-evaluation-failed` and `posture-evaluation-succeeded` with a single `posture-evaluated` event under "Posture and Runtime Events", carrying a REQUIRED `result` of `failed` or `succeeded`; retained the failure-only `reason` enum and clarified its correlation with failed `credential-rotated` events.
- Renamed the "Trust and Federation Events" section to "Trust and Policy Events", moved `policy-changed` into it, and removed the now-empty "Policy Evaluation Events" section.
- Renamed the "Runtime Posture Events" section to "Posture and Runtime Events".
- Renamed `workload-baseline-changed` to `workload-execution-environment-changed` to clarify that the event signals a change to a workload's runtime execution environment or deployment context.
- Removed the `image_update` and `config_change` reasons from `workload-execution-environment-changed` and removed metadata changes from the event's scope.

-03

- Updated the WIMSE Architecture, AIMS, and OAuth Client ID Metadata Document references to their current rolling Internet-Draft URLs and author metadata.
- Standardized the draft on American English spelling.
- Made `effective_at` a Common Optional Claim available to any WISE event, removed the redundant per-event definitions, and clarified that, when omitted, a change is effective as of the containing SET's `iat` value and is processed immediately upon receipt.
- Made `event_timestamp` a Common Mandatory Claim, removed the redundant per-event definitions, and updated all event examples to include it.
- Completed the trust and federation lifecycle. Split the omnibus `trust-anchor-changed` event into explicit `trust-anchor-added`, `trust-anchor-rotated`, and `trust-anchor-revoked` events, and added `trust-domain-federation-established` and `trust-domain-federation-updated` to complement `trust-domain-federation-revoked`.
- Added an optional `key_details` object using the standard JWK parameter names `kty`, `alg`, `use`, and `crv`, with values from the applicable JOSE registries, to `trust-anchor-added`, supporting the addition of a new algorithm (e.g., ECDSA alongside RSA) without rotation.
- Added an informative reference for condition-bounded credentials and cited it in the credential freshness models discussion.
- Corrected the description of condition-liveness to state that it removes (rather than reduces) the local deprovisioning window for conditions the endpoint can evaluate itself.
- Noted in Compromise Response that credential rejection only takes effect at the next operation, so Receivers holding active connections with an affected workload should also terminate them (best-effort).
- Added a general "Correlating Related Events" rule: a Transmitter SHOULD set a shared `txn` claim across all SETs describing one underlying occurrence, regardless of event type (replacing the per-event guidance). Added conditional pairing guidance to `workload-compromised`.
- Generalized the Compromise Response rule to apply to any event carrying a `compromise` or `key_compromise` signal, rather than an enumerated list of event types.
- Defined a minimal interoperable key set (`region`, `zone`, `platform`, `cluster`, `image`, each optional) for the `previous_context`/`current_context` fields of `workload-baseline-changed`, and allowed profile-specific extension.
- Expanded the `key_storage` values on `credential-issued` from the binary `hardware`/`software` to `software`, `software_encrypted`, `hardware_tpm`, `hardware_hsm`, `hardware_secure_enclave`, `vaulted`, and `unknown`, so a Receiver can distinguish key-protection strength when making a trust decision.
- Added a single Privacy Considerations note covering over-disclosure in all free-form/descriptive fields (`key_storage_ecosystem`, `previous_context`, `current_context`), replacing per-field guidance.
- Clarified the distinction between `credential-renewal-failure` (operational outcome, multiple causes) and `posture-evaluation-failed` (a security-critical signal in its own right), and how the two relate when posture is the cause of a renewal failure.
- Made `reason_admin`/`reason_user` usage consistent: removed the redundant per-event listings and rely on the Common Optional Claims section, which now states the claims are not repeated per event and any event MAY carry them.
- Replaced the free-text `reason` on `posture-evaluation-failed` with an enum (`platform_integrity_failed`, `attestation_invalid`, `image_mismatch`, `configuration_noncompliant`, `policy_denied`), matching the enum style of sibling events.
- Renamed the `credential-compromise` event to `credential-compromised` for tense consistency with the other credential events (`-issued`, `-rotated`, `-revoked`) and `workload-compromised`.
- Added a non-normative example to every event that lacked one, so all event types now have consistent example coverage (and confirmed policy events use the trust-domain subject).
- Established a "WISE Event Types" IANA registry (Specification Required) with all defined event types as initial registrations, replacing the "no new registrations" statement.
- Added `workload-degraded` and `workload-restored` events for adaptive resilience: an advisory, coarse graduated `trust_level` signal for intentionally reducing a workload's trust without suspension, and its reverse. Clarified their complementarity with `anomalous-behavior-detected`, `workload-compromised`, and `workload-disabled`.
- Added the Named Information (`ni`) URI scheme (RFC 6920) as an optional subject identifier for identifying workloads by build/image digest, with a note that a digest identifies an artifact rather than a single workload instance.
- Replaced the free-form `change_description` field on `trust-domain-federation-updated` and the policy-change events with the localizable `reason_admin`/`reason_user` common claims, for consistency with the CAEP-aligned pattern.
- Carried the subject in the top-level `sub_id` claim (RFC 9493 format, per SSF) instead of a nonstandard nested `subject` member, updating every example; and specified that policy events take the trust-domain subject.

-02

- Removed the Bound Key Lifecycle Events (`bound-key-issued`, `bound-key-rotated`, `bound-key-revoked`); key compromise and rotation are now handled through the credential events, and a `key_compromise` reason was added to `credential-revoked`. The `key_storage` and `key_storage_ecosystem` attributes moved to `credential-issued`.
- Aligned terminology with the WIMSE architecture: replaced "Identity Server" with "Credential Service", and "machine identity lifecycle" with "workload identity lifecycle".
- Softened the single-authority language and aligned it with WIMSE (a trust domain maps to one or more trust anchors).
- Renamed "Workload Identity State Events" to "Workload Lifecycle Events" and clarified that credential cancellation and issuance are conveyed by separate companion events.
- Added a "Common Optional Claims" section aligning `reason_admin`, `reason_user`, and `initiating_entity` with CAEP as localizable objects.
- Promoted RISC to a normative reference and refreshed the RISC, CAEP, and SSF references to their final 1.0 versions.
- Added normative references for WPT, RFC 5646, RFC 7523, RFC 8705, and RFC 9325, and cited RFC 7517 for the JWK Set.
- Replaced the TLS 1.2 requirement with a normative reference to the TLS recommendations in RFC 9325.
- Normalized enum values to use underscores while keeping event type names hyphenated.

-01

- Added Supply Chain Events

-00

- Initial draft.

