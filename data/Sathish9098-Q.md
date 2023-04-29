Report contents changed:  # LOW FINDINGS

| LOW COUNT| ISSUES | INSTANCES|
|-------|-----|--------|
| [L-1]| Use abi.encode to convert safest way from uint values to bytes  | 3 |
| [L-2]| Loss of precision due to rounding | 1 |
| [L-3]| Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256  | 11 |
| [L-4]| MIXING AND OUTDATED COMPILER  | 7 |
| [L-5]| abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()  | 7 |
| [L-6]| Lack of Sanity/Threshold/Limit Checks  | 2 |
| [L-7]| Function Calls in Loop Could Lead to Denial of Service | 10 |
| [L-8]| Project Upgrade and Stop Scenario should be  | - |
| [L-9]| Front running attacks by the onlyOwner  | 3 |
| [L-10]| Use BytesLib.sol library to safely covert bytes to uint256  | 2 |
| [L-11]| Avoid infinite loops whenever possible  | 2 |
| [L-12]| In the constructor, there is no return of incorrect address identification  | 6 |
| [L-13]| Even with the onlyOwner or owner_only modifier, it is best practice to use the re-entrancy pattern  | 3 |

 # NON CRITICAL FINDINGS

| NC COUNT| ISSUES | INSTANCES|
|-------|-----|--------|
| [NC-1]| immutable should be uppercase  | 4 |
| [NC-2]| Missing NATSPEC  | - |
| [NC-3]| For functions, follow Solidity standard naming conventions (internal function style rule) | 28 |
| [NC-4]| Need Fuzzing test for unchecked  | 5 |
| [NC-5]| Remove commented out code  | - |
| [NC-6]| Inconsistent method of specifying a floating pragma  | 8 |
| [NC-7]| NO SAME VALUE INPUT CONTROL  | 1 |
| [NC-8]| Constant redefined elsewhere  | - |
| [NC-9]| According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword| 3 |
| [NC-10]| Use SMTChecker  | - |
| [NC-11]| Constants on the left are better | 20 |
| [NC-12]| Assembly Codes Specific – Should Have Comments  | 18 |
| [NC-13]| Take advantage of Custom Error’s return value property  | 4 |
| [NC-14]| Use constants instead of using numbers directly without explanations  | 6 |
| [NC-15]| Shorthand way to write if / else statement | 4 |
| [NC-16]| For critical changes should emit both old and new values  | 1 |
| [NC-17]| Don't use named return variables its confusing  | 8 |
| [NC-18]| NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES  | 7 |
| [NC-19]| Constants should be in uppercase   | 7 |
| [NC-20]| TYPOS  | 4 |
| [NC-21]| Contracts should have full test coverage  | - |
| [NC-22]| Use named parameters for mapping type declarations  | 3 |
| [NC-23]| File does not contain an SPDX Identifier   | 4 |
| [NC-24]| declaration shadows an existing declaration  | - |
| [NC-25]| Event is missing indexed fields  | 2 |


##

## [L-1] Use abi.encode to convert safest way from uint values to bytes

### DRAWBACKS
Now the protocol uses direct conversion way this is not safe. bytes(value) to convert a uint to bytes is not considered a safe way because it creates an uninitialized byte array of length. This means that the contents of the byte array are undefined and may contain sensitive data from previous memory usage, which could result in security vulnerabilities.

### BENIFITS of abi.encode

In Solidity, the safest way to convert a uint to bytes is to use the abi.encode function. This function will encode the uint as a byte array using the ABI encoding rules, which ensures that the output is a deterministic and standardized representation of the uint value.

```solidity
FILE: 2023-04-ens/contracts/utils/NameEncoder.sol

27:  dnsName[i + 1] = bytes1(labelLength);
49:  dnsName[0] = bytes1(labelLength);

```
[NameEncoder.sol#L27](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L27)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

376: return bytes32(ret << (256 - bitlen));


```
[BytesUtils.sol#L376](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L376)

##

## [L-2] Loss of precision due to rounding

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

52: q = r1 / r2;

```
[EllipticCurve.sol#L52](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L52)

##

## [L-3] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256

Using the SafeCast library can help prevent unexpected errors in your Solidity code and make your contracts more secure

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

160: if (!RRUtils.serialNumberGte(rrset.expiration, uint32(now))) {
161: revert SignatureExpired(rrset.expiration, uint32(now));
166: if (!RRUtils.serialNumberGte(uint32(now), rrset.inception)) {
167: revert SignatureNotValidYet(rrset.inception, uint32(now));

```
[DNSSECImpl.sol#L160](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L160)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

430: return uint16(ac1);

```
[RRUtils.sol#L430](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L430)

> When ever we convert int256 to uint256 or uint256 to int256 we should use OpenZeppelin’s safecast to avoid unexpected errors

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

56: if (t1 < 0) return (m - uint256(-t1));
58: return uint256(t1);

```
[EllipticCurve.sol#L56-L58](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L56-L58)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

92: int256 diff = int256(a & mask) - int256(b & mask);
99: return int256(len) - int256(otherlen);
183: return uint8(self[idx]);
344: decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);

```
[BytesUtils.sol#L92](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L92)

### Recommended Mitigation Steps:
Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256.

##

## [L-4] MIXING AND OUTDATED COMPILER

The pragma version used are: 0.8.0

The minimum required version must be 0.8.17; otherwise, contracts will be affected by the following important bug fixes:

0.8.14:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

0.8.15

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

0.8.16

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

0.8.17
Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.
Apart from these, there are several minor bug fixes and improvements

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

2: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

1: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnssec-oracle/SHA1.sol

1: pragma solidity >=0.8.4;

FILE: 2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol

2: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnsregistrar/RecordParser.sol

2: pragma solidity ^0.8.11;

FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

1: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/RSAVerify.sol

1: pragma solidity ^0.8.4;

```
##

## [L-5] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). "Unless there is a compelling reason, abi.encode should be preferred". If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead. If all arguments are strings and or bytes, bytes.concat() should be used instead

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

222: keccak256(
                abi.encodePacked(parentNode, name.keccak(idx, separator - idx))
            );

```
[OffchainDNSResolver.sol#L222-L224](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L222-L224)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

119: bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
151: bytes32 node = keccak256(abi.encodePacked(parentNode, labelHash));
185: node = keccak256(abi.encodePacked(parentNode, label));

```
[DNSRegistrar.sol#L119](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L119)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

12: bytes memory input = abi.encodePacked(
            uint256(base.length),
            uint256(exponent.length),
            uint256(modulus.length),
            base,
            exponent,
            modulus
        );

```
[ModexpPrecompile.sol#L12-L19`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L12-L19)

```solidity
FILE: 2023-04-ens/contracts/utils/NameEncoder.sol

28: node = keccak256(
                        abi.encodePacked(
                            node,
                            bytesName.keccak(i + 1, labelLength)
                        )
                    );

45: node = keccak256(
            abi.encodePacked(node, bytesName.keccak(0, labelLength))
        );

```
[NameEncoder.sol#L28-L33](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L28-L33)

##

## [L-6] Lack of Sanity/Threshold/Limit Checks

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation as well as zero address checks for critical changes. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

64: function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
        algorithms[id] = algo;
        emit AlgorithmUpdated(id, address(algo));
    }

75: function setDigest(uint8 id, Digest digest) public owner_only {
        digests[id] = digest;
        emit DigestUpdated(id, address(digest));
    }

```
[DNSSECImpl.sol#L64-L78](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L64-L78)

##

## [L-7] Function Calls in Loop Could Lead to Denial of Service

Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions


```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

while (counts > othercounts) {
            prevoff = off;
            off = progress(self, off);
            counts--;
        }

while (othercounts > counts) {
            otherprevoff = otheroff;
            otheroff = progress(other, otheroff);
            othercounts--;
        }

        // Compare the last nonequal labels to each other
        while (counts > 0 && !self.equals(off, other, otheroff)) {
            prevoff = off;
            off = progress(self, off);
            otherprevoff = otheroff;
            otheroff = progress(other, otheroff);
            counts -= 1;
        }

```
[RRUtils.sol#L291-L295](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L291-L295)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

118: for (uint256 i = 0; i < input.length; i++) {
            RRUtils.SignedSet memory rrset = validateSignedSet(
                input[i],
                proof,
                now
            );
            proof = rrset.data;
            inception = rrset.inception;
        }

260: for (; !proof.done(); proof.next()) {
            bytes memory proofName = proof.name();
            if (!proofName.equals(rrset.signerName)) {
                revert ProofNameMismatch(rrset.signerName, proofName);
            }

            bytes memory keyrdata = proof.rdata();
            RRUtils.DNSKEY memory dnskey = keyrdata.readDNSKEY(
                0,
                keyrdata.length
            );
            if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {
                return;
            }



```
[DNSSECImpl.sol#L118-L126](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L118-L126)

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L336-L360

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L380-L404

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L325-L327

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361-L362

##

## [L-8] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation. This can also be called an ” EMERGENCY STOP (CIRCUIT BREAKER) PATTERN “.

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


##

## [L-9] Front running attacks by the onlyOwner


```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }

```
[DNSRegistrar.sol#L80-L83](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

64: function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
        algorithms[id] = algo;
        emit AlgorithmUpdated(id, address(algo));
    }

75: function setDigest(uint8 id, Digest digest) public owner_only {
        digests[id] = digest;
        emit DigestUpdated(id, address(digest));
    }

```
[DNSSECImpl.sol#L64-L67](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L64-L67)

##

## [L-10] Use BytesLib.sol library to safely covert bytes to uint256

Use [toUint256()](https://github.com/GNSPS/solidity-bytes-utils/blob/master/contracts/BytesLib.sol) safely convert bytes to uint256 instead of plain way of conversion

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

34: return [uint256(data.readBytes32(0)), uint256(data.readBytes32(32))];
41: return [uint256(data.readBytes32(4)), uint256(data.readBytes32(36))];

```
[P256SHA256Algorithm.sol#L34](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L34)

##

## [L-11] Avoid infinite loops whenever possible

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

> This r2 != 0 condition can be always true cause unbounded loop. This condition only fails if r2 is exactly equal to 0   

51: while (r2 != 0) {
                q = r1 / r2;
                (t1, t2, r1, r2) = (t2, t1 - int256(q) * t2, r2, r1 - q * r2);
            }

```
[EllipticCurve.sol#L51-L54](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L51-L54)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

while (true) {
            assert(idx < self.length);
            uint256 labelLen = self.readUint8(idx);
            idx += labelLen + 1;
            if (labelLen == 0) {
                break;
            }
        }

```
[RRUtils.sol#L24-L31](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L24-L31)

##

## [L-12] In the constructor, there is no return of incorrect address identification

In case of incorrect address definition in the constructor , there is no way to fix it because of the variables are immutable.

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

44: ens = _ens;
45: oracle = _oracle;

```
[OffchainDNSResolver.sol#L44-L45](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L44-L45)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

62: previousRegistrar = _previousRegistrar;
63: resolver = _resolver;
64: oracle = _dnssec;
67: ens = _ens;

```
[DNSRegistrar.sol#L63-L65](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L63-L65)

### Recommended Mitigations:

require(_ens!=address(0), " zero address");

##

## [L-13] Even with the onlyOwner or owner_only modifier, it is best practice to use the re-entrancy pattern

It's still good practice to apply the reentry model as a precautionary measure in case the code is changed in the future to remove the onlyOwner modifier or the contract is used as a base contract for other contracts.

Using the reentry modifier provides an additional layer of security and ensures that your code is protected from potential reentry attacks regardless of any other security measures you take.

So even if you followed the "check-effects-interactions" pattern correctly, it's still considered good practice to use the reentry modifier


```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }

```
[DNSRegistrar.sol#L80-L83](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

64: function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
        algorithms[id] = algo;
        emit AlgorithmUpdated(id, address(algo));
    }

75: function setDigest(uint8 id, Digest digest) public owner_only {
        digests[id] = digest;
        emit DigestUpdated(id, address(digest));
    }

```
[DNSSECImpl.sol#L64-L67](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L64-L67)

Recommended Mitigation:

```solidity

  modifier noReentrant() {
        require(!locked, "Reentrant call");
        locked = true;
        _;
        locked = false;
    }

function setPublicSuffixList(PublicSuffixList _suffixes) public  onlyOwner noReentrant  {

```

##

# NON CRITICAL FINDINGS

##

## [NC-1] immutable should be uppercase

```solidity
FILE : 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

37: ENS public immutable ens;
38: DNSSEC public immutable oracle;

```
[OffchainDNSResolver.sol#L37-L38](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L37-L38)

### Recommended Mitigation

```solidity
- 38: DNSSEC public immutable oracle;
+ 38: DNSSEC public immutable ORACLE;
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L26-L27

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L29-L30


##

## [NC-2] Missing NATSPEC

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L22-L27

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L49-L52

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L65-L69

##

## [NC-3] For functions, follow Solidity standard naming conventions (internal function style rule)

### Description
The above codes don’t follow Solidity’s standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

```solidity
File: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

136: function parseRR(
        bytes memory data,
        uint256 idx,
        uint256 lastIdx
    ) internal view returns (address, bytes memory) {

162: function readTXT(
        bytes memory data,
        uint256 startIdx,
        uint256 lastIdx
    ) internal pure returns (bytes memory) {

173: function parseAndResolve(
        bytes memory nameOrAddress,
        uint256 idx,
        uint256 lastIdx
    ) internal view returns (address) {

190: function resolveName(
        bytes memory name,
        uint256 idx,
        uint256 lastIdx
    ) internal view returns (address) {

209: function textNamehash(
        bytes memory name,
        uint256 idx,
        uint256 lastIdx
    ) internal view returns (bytes32) {

```
[OffchainDNSResolver.sol#L136-L140](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L136-L140)

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L14-L18

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L7-L11

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L30-L32

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L37-L39

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L9-L11

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L11-L15

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L68-L72

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L19-L22

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L46-L50

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L66-L70

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L13-L17

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L32-L35

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L52-L59

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L111-L117

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L129-L134

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L148-L152

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L164-L167

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L179-L182

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L332-L336

##

## [NC-4] Need Fuzzing test for unchecked

```solidity
FILE: 2023-04-ens/contracts/utils/NameEncoder.sol

24:  unchecked {

FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

380:   unchecked {
336:   unchecked {

FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

284:  unchecked {

FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

41:  unchecked {

```
##

## [NC-5] Remove commented out code


```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

358: *     function computeKeytag(bytes memory data) internal pure returns (uint16) {
359: *         uint ac;
360: *         for (uint i = 0; i < data.length; i++) {
361: *             ac += i & 1 == 0 ? uint16(data.readUint8(i)) << 8 : data.readUint8(i);
362: *         }
363: *         return uint16(ac + (ac >> 16));
364: *     }

```
[RRUtils.sol#L358-L364](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L358-L364)


##

## [NC-6] Inconsistent method of specifying a floating pragma

Some files use >=, some use ^. The instances below are examples of the method that has the fewest instances for a specific version. Note that using >= without also specifying <= will lead to failures to compile, or external project incompatability, when the major version changes and there are breaking-changes, so ^ should be preferred regardless of the instance counts

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

2: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

1: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnssec-oracle/SHA1.sol

1: pragma solidity >=0.8.4;

FILE: 2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol

2: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnsregistrar/RecordParser.sol

2: pragma solidity ^0.8.11;

FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

1: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/RSAVerify.sol

1: pragma solidity ^0.8.4;

```
##

## [NC-7] NO SAME VALUE INPUT CONTROL

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }

```
[DNSRegistrar.sol#L80-L83](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83)

##

## [NC-8] Constant redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated.

A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

29: uint16 constant CLASS_INET = 1;
30: uint16 constant TYPE_TXT = 16;

FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

72: uint256 constant RRSIG_TYPE = 0;
73: uint256 constant RRSIG_ALGORITHM = 2;
74: uint256 constant RRSIG_LABELS = 3;
75: uint256 constant RRSIG_TTL = 4;
76: uint256 constant RRSIG_EXPIRATION = 8;
77: uint256 constant RRSIG_INCEPTION = 12;
78: uint256 constant RRSIG_KEY_TAG = 16;
79: uint256 constant RRSIG_SIGNER_NAME = 18;
210: uint256 constant DNSKEY_FLAGS = 0;
211: uint256 constant DNSKEY_PROTOCOL = 2;
212: uint256 constant DNSKEY_ALGORITHM = 3;
213: uint256 constant DNSKEY_PUBKEY = 4;

236: uint256 constant DS_KEY_TAG = 0;
237: uint256 constant DS_ALGORITHM = 2;
238: uint256 constant DS_DIGEST_TYPE = 3;
239: uint256 constant DS_DIGEST = 4;

FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

21: uint256 constant a =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFC;
23: uint256 constant b =
        0x5AC635D8AA3A93E7B3EBBD55769886BC651D06B0CC53B0F63BCE3C3E27D2604B;
25: uint256 constant gx =
        0x6B17D1F2E12C4247F8BCE6E563A440F277037D812DEB33A0F4A13945D898C296;
27: uint256 constant gy =
        0x4FE342E2FE1A7F9B8EE7EB4A7C0F9E162BCE33576B315ECECBB6406837BF51F5;
29: uint256 constant p =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFF;
30: uint256 constant n =
        0xFFFFFFFF00000000FFFFFFFFFFFFFFFFBCE6FAADA7179E84F3B9CAC2FC632551;

32: uint256 constant lowSmax =
        0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0;

FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

27: uint16 constant DNSCLASS_IN = 1;
29: uint16 constant DNSTYPE_DS = 43;
30: uint16 constant DNSTYPE_DNSKEY = 48;
32: uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;
```

##

## [NC-9] According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword


```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

45: mapping(uint8 => Algorithm) public algorithms;
46: mapping(uint8 => Digest) public digests;

```
[DNSSECImpl.sol#L45-L46](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L45-L46)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

32: mapping(bytes32 => uint32) public inceptions;

```
[DNSRegistrar.sol#L32](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L32)


## [NC-10] Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

##

## [NC-11] Constants on the left are better

If you use the constant first you support structures that veil programming errors. And one should only produce code either to add functionality, fix an programming error or trying to support structures to avoid programming errors (like design patterns).

https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L178

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L179

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L294

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L51

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

145:  if (a != 0) {
148:  if (b != 0) {
392:  if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {
410:  if (P[2] == 0) {
340:  if (scalar == 0) {
342:  } else if (scalar == 1) {
344:  } else if (scalar == 2) {
355:  if (scalar % 2 == 0) {
364:  if (scalar % 2 == 1) {

```
[EllipticCurve.sol#L145](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L145)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

28:  if (labelLen == 0) {
64:  if (labelLen == 0) {
312: if (off == 0) {
315: if (otheroff == 0) {
```
[RRUtils.sol#L28](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L28)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

353: if (len % 8 == 0) {
356: } else if (len % 8 == 2) {
360: } else if (len % 8 == 4) {
364: } else if (len % 8 == 5) {
368: } else if (len % 8 == 7) {

```
[BytesUtils.sol#L353](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L353)

##

## [NC-12] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone


```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

23: assembly {

```
[ModexpPrecompile.sol#L23](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L23)

```solidity
FILE: 2023-04-ens/contracts/utils/HexUtils.sol

17:  assembly {

```
[HexUtils.sol#L17](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L17)

```solidity
FILE: FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

386: assembly {

```
[RRUtils.sol#L386](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L386)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/SHA1.sol

7: assembly {

```
[SHA1.sol#L7](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/SHA1.sol#L7)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

19:  assembly {
73:  assembly {
80:  assembly {
197: assembly {
213: assembly {
229: assembly {
245: assembly {
267: assembly {
276: assembly {
286: assembly {
311: assembly {


```
[BytesUtils.sol#L19](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L19)

##

## [NC-13] Take advantage of Custom Error’s return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly


```solidity
2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

34: error NoOwnerRecordFound();
35: error PreconditionNotMet();
36: error StaleProof();
```
[DNSRegistrar.sol#L36-L37](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L36-L37)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

38:  error InvalidRRSet();

```
[DNSSECImpl.sol#L38](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L38)


##

## [NC-14] Use constants instead of using numbers directly without explanations


```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

144: if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {
149: uint256 lastTxtIdx = txt.find(5, txt.length - 5, " ");
151: address dnsResolver = parseAndResolve(txt, 5, txt.length);
154: address dnsResolver = parseAndResolve(txt, 5, lastTxtIdx);

```
[OffchainDNSResolver.sol#L144](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L144)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

33: require(data.length == 64, "Invalid p256 signature length");
40: require(data.length == 68, "Invalid p256 key length");

```
[P256SHA256Algorithm.sol#L33](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L33)

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L25-L36



##

## [NC-15] Shorthand way to write if / else statement

The normal if / else statement can be refactored in a shorthand way to write it:

Increases readability
Shortens the overall SLOC
## [NC-16] For critical changes should emit both old and new values

Emitting both old and new values for critical changes is a good practice in Solidity, as it provides a way for external parties (such as other smart contracts or off-chain applications) to track and verify the changes made to a smart contract state.

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }

```
[DNSRegistrar.sol#L80-L83](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83)

##

## [NC-17] Don't use named return variables its confusing

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

113: function _claim(
        bytes memory name,
        DNSSEC.RRSetWithSignature[] memory input
    ) internal returns (bytes32 parentNode, bytes32 labelHash, address addr) {

166: function enableNode(bytes memory domain) public returns (bytes32 node) {

174: function _enableNode(
        bytes memory domain,
        uint256 offset
    ) internal returns (bytes32 node) {


```
[DNSRegistrar.sol#L133-L136](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L133-L136)

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L14-L21

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L7-L11

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L11-L15

##

## [18] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

2: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

1: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnssec-oracle/SHA1.sol

1: pragma solidity >=0.8.4;

FILE: 2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol

2: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnsregistrar/RecordParser.sol

2: pragma solidity ^0.8.11;

FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

1: pragma solidity ^0.8.4;

FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/RSAVerify.sol

1: pragma solidity ^0.8.4;

```

##

## [NC-19] Constants should be in uppercase

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

21: uint256 constant a =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFC;
23: uint256 constant b =
        0x5AC635D8AA3A93E7B3EBBD55769886BC651D06B0CC53B0F63BCE3C3E27D2604B;
25: uint256 constant gx =
        0x6B17D1F2E12C4247F8BCE6E563A440F277037D812DEB33A0F4A13945D898C296;
27: uint256 constant gy =
        0x4FE342E2FE1A7F9B8EE7EB4A7C0F9E162BCE33576B315ECECBB6406837BF51F5;
29: uint256 constant p =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFF;
30: uint256 constant n =
        0xFFFFFFFF00000000FFFFFFFFFFFFFFFFBCE6FAADA7179E84F3B9CAC2FC632551;

32: uint256 constant lowSmax =
        0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0;
```
[EllipticCurve.sol#L21-L35](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21-L35)

##

## [NC-20] TYPOS

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

/// @audit codepoints => code points

- 43: *      on unicode codepoints.
+ 43: *      on unicode code points.

FILE: 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

/// @audit bitshifting=> bit shifting
/// @audit Naive => Native

- 356: * from the input string, with some mild bitshifting. Here's a Naive solidity implementation:
- 356: * from the input string, with some mild bit shifting. Here's a Native solidity implementation:

FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

/// @audit canonicalised => MEANING LESS WORD

135: *        data, followed by a series of canonicalised RR records that the signature


```
##

## [NC-21] Contracts should have full test coverage

While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit

```
What is the overall line coverage percentage provided by your tests?:  90

```

##

## [NC-22] Use named parameters for mapping type declarations

Consider using named parameters in mappings (e.g. mapping(address account => uint256 balance)) to improve readability. This feature is present since Solidity 0.8.18.

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

45: mapping(uint8 => Algorithm) public algorithms;
46: mapping(uint8 => Digest) public digests;

```
[DNSSECImpl.sol#L45-L46](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L45-L46)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

32: mapping(bytes32 => uint32) public inceptions;

```
[DNSRegistrar.sol#L32](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L32)

##

## [NC-23] File does not contain an SPDX Identifier

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/SHA1.sol#L1-L3

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L1-L3

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L1-L9

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L1-L19

##

## [NC-24] declaration shadows an existing declaration

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

30:   address public immutable resolver;
104:  address resolver,
159:  function resolver(

192: address owner,
143: function owner(

```

##

## [NC-25] Event is missing indexed fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (threefields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

47: event Claim(
        bytes32 indexed node,
        address indexed owner,
        bytes dnsname,
        uint32 inception
    );
53: event NewPublicSuffixList(address suffixes);

```
[DNSRegistrar.sol#L47-L53](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L47-L53)








[L‑01]	Events are missing sender information	6
[L‑02]	The owner is a single point of failure and a centralization risk	27
[L‑03]	Use Ownable2Step's transfer function rather than Ownable's for transfers of ownership	2
[L‑04]	Unsafe downcast	3
[L‑05]	Loss of precision	4
[L‑06]	Upgradeable contract not initialized	6
[L‑07]	Array lengths not checked	3
[L‑08]	Missing checks for address(0x0) when assigning values to address state variables	1
[L‑09]	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	2
[L‑10]	Use Ownable2Step rather than Ownable	3

[N‑01]	Constants in comparisons should appear on the left side	118
[N‑02]	Variables need not be initialized to zero	15
[N‑03]	Imports could be organized more systematically	13
[N‑04]	Large numeric literals should use underscores for readability	14
[N‑05]	Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions	1
[N‑06]	Import declarations should import specific identifiers, rather than the whole file	71
[N‑07]	Missing initializer modifier on constructor	1
[N‑08]	Unused file	1
[N‑09]	The nonReentrant modifier should occur before all other modifiers	9
[N‑10]	Adding a return statement when the function defines a named return variable, is redundant	2
[N‑11]	public functions not called by the contract should be declared external instead	1
[N‑12]	constants should be defined rather than using magic numbers	56
[N‑13]	Cast is more restrictive than the type of the variable being assigned	1
[N‑14]	Numeric values having to do with time should use time units for readability	1
[N‑15]	Use a more recent version of solidity	3
[N‑16]	Expressions for constant values such as a call to keccak256(), should use immutable rather than constant	2
[N‑17]	Constant redefined elsewhere	6
[N‑18]	Inconsistent spacing in comments	24
[N‑19]	Lines are too long	23
[N‑20]	Variable names that consist of all capital letters should be reserved for constant/immutable variables	2
[N‑21]	Typos	21
[N‑22]	Misplaced SPDX identifier	1
[N‑23]	File is missing NatSpec	1
[N‑24]	NatSpec is incomplete	41
[N‑25]	Event is missing indexed fields	23
[N‑26]	Consider using delete rather than assigning zero to clear values	1
[N‑27]	Avoid the use of sensitive terms	31
[N‑28]	Contracts should have full test coverage	1
[N‑29]	Large or complicated code bases should implement invariant tests	1
[N‑30]	Function ordering does not follow the Solidity style guide	7
[N‑31]	Contract does not follow the Solidity style guide's suggested layout ordering	7