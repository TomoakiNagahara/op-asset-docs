# Cookie

## Overview

In the ONEPIECE Framework, cookies are not stored in plain form.

The framework transforms the cookie key and encrypts the cookie value before sending the cookie to the browser.

This is intended to make client-side reading and modification more difficult.

## Key Handling

The cookie key is not sent in its original application-facing form.

Before storage, the framework converts the key with:

```php
Hasha1($key, 16)
```

In the current implementation, this hash uses the App ID as the default salt.

That means:

- the browser does not receive the original key name
- the resulting cookie key depends on the application identity

Strictly speaking, this is hashing, not encryption.

## Value Handling

The cookie value is:

1. serialized
2. encrypted
3. sent to the browser

When reading:

1. the same transformed key is used
2. the stored value is decrypted
3. the result is unserialized

This means the cookie value is intentionally hidden from plain client-side inspection.

## Purpose

The design intention is:

- make cookie values difficult to read directly on the client side
- make cookie modification more difficult on the client side
- avoid exposing original internal cookie key names
- tie cookie handling to the application identity through App ID

## Relationship to App ID

The App ID is used in two related places:

- as the default salt for cookie key hashing
- as part of the encryption basis used by the framework encryption layer

Because of that, cookie handling is tied to the application identity of the framework instance.

## [DOC-RISK] Important Precision

The current design makes client-side tampering more difficult, but it should not be described too strongly as absolute tamper-proof protection.

More precise wording is:

- the framework obscures the key name
- the framework encrypts the stored value
- this makes casual reading or casual modification difficult

## [DOC-RISK] Why Absolute Tamper Detection Cannot Be Claimed From This Code Alone

From the current code, it is not enough to claim complete tamper detection.

The main reasons are:

### 1. App ID exposure or prediction

If the App ID is leaked or can be predicted, the protection level becomes weaker because:

- cookie key derivation depends on App ID
- encryption source material also depends on App ID unless overridden

### 2. Encryption alone is not the same as explicit integrity verification

The current implementation uses encrypted storage, but this code alone does not show a separate MAC or signature verification step for the cookie payload.

That means the code clearly provides confidentiality-oriented protection, but it does not by itself justify claiming complete integrity protection.

### 3. Failed modification and guaranteed detection are different claims

In practice, arbitrary client-side modification may often break decryption or unserialization.

However, “modification becomes invalid” and “the system has cryptographically guaranteed tamper detection” are not exactly the same claim.

## Practical Interpretation

The safest practical interpretation is:

- the framework is designed to make cookie reading and modification difficult
- the design is useful for raising the bar against casual client-side tampering
- this documentation should avoid overstating the mechanism as absolute tamper-proof security unless stronger integrity guarantees are explicitly implemented

## Summary

In the ONEPIECE Framework:

- cookie keys are hashed with App ID influence
- cookie values are encrypted before being stored in the browser
- this is intended to make client-side reading and modification difficult

At the same time, the current implementation should be described as tamper-resistant rather than as provably tamper-proof from this code alone.
