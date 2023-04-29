# GAS OPTIMIZATION

| Gas Count| Issues| Instances| Gas Saved|
|-------|-----|--------|--------|
| [G-1] | State variable value only set in the constructor can be declared as a constants to save large volume of the gas   | 1 | 90000 |
| [G-2] | State variables only set in the constructor should be declared immutable  | 1 | 20000 |
| [G-3] | Using storage instead of memory for structs/arrays saves gas  | 3 | 6300 |
| [G-4] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate  | 1 | - |
| [G-5] | For events use 3 indexed rule to save gas  | 3 | - |
| [G-6] | Lack of input value checks cause a redeployment if any human/accidental errors | 4 | - |
| [G-7] | Use nested if and, avoid multiple check combinations  | 2 | 18 |
| [G-8] | Unnecessary look up in if condition  | 7 | - |
| [G-9] | Don't declare the variable inside the loops  | 3 | - |
| [G-10] | Functions should be used instead of modifiers to save gas  | 1 | - |
| [G-11] | Sort Solidity operations using short-circuit mode  | 5 | - |
| [G-12] | Use assembly to check for address(0)  | 7 | 42 |
| [G-13] | Shorthand way to write if / else statement can reduce the deployment cost| 4 | - |
| [G-14] | The Less gas consuming condition checks should be on top  | 1 | - |
| [G-15] | nternal functions not called by the contract should be removed to save deployment gas  | 3 | - |
| [G-16] | Modifiers or private functions only called once can be inlined to save gas | 2 | 80 |
| [G-17] | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS  | 12 | - |
| [G-18] | Use constants instead of type(uintx).max  | 5 | - |
| [G-19] | Instead of calculating bytes32(0) every time inside the contract its possible to use constants to reduce the gas cost   | 1 | - |
| [G-20] | Structs can be packed into fewer storage slots  | 1 | 40000 |


### TOTAL INSTANCES : 67

## APROXIMATE GAS SAVED: 156000 gas 

##

## [G-1] State variable value only set in the constructor can be declared as a constants to save large volume of the gas

> Instances (1)

> Approximate Gas Saved : (4000 gas) (As per my remix test 90000 gas)

gatewayURL string variable value is only set inside the constructor. The value is not changed anywhere inside the contract. This gatewayURL can be declared as constant to save large volume of gas

As per [Remix gas test](https://gist.github.com/sathishpic22/0079888aca5a6fe626b74f0063546138) approximately possible to saves 90000 gas

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

39:  string public gatewayURL;

43: constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
        ens = _ens;
        oracle = _oracle;
        gatewayURL = _gatewayURL;
    }

```
[OffchainDNSResolver.sol#L39-L47](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L39-L47)

##

## [G-2] State variables only set in the constructor should be declared immutable

> Instances(1)

> Approximate Gas saved : 20000 gas

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

While strings are not value types, and therefore cannot be immutable/constant if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract abstract with virtual functions for the string accessors, and having a child contract override the functions with the hard-coded implementation-specific values


> In parent contract DNSSEC.sol the variable "anchors" should be declared immutable . The anchors value is only set inside the DNSSECImpl contract constructor

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

55:  anchors = _anchors;

```
[DNSSECImpl.sol#L55](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L55)

## [G-3] Using storage instead of memory for structs/arrays saves gas

> Instances(3)

> Approximate gas saved: 6300 gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

53: string[] memory urls = new string[](1);


```
[OffchainDNSResolver.sol#L53](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L53)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

307: bytes memory ret = new bytes(len);

```
[BytesUtils.sol#L307](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L307)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

408: uint256[3] memory P = addAndReturnProjectivePoint(x1, y1, x2, y2);
```
[EllipticCurve.sol#L408](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L408)

##

## [G-4] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

> Instances(1)

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key’s keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

45: mapping(uint8 => Algorithm) public algorithms;
46: mapping(uint8 => Digest) public digests;

```
[DNSSECImpl.sol#L45-L46](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L45-L46)

##

## [G-5] For events use 3 indexed rule to save gas

> Instances(2)

Need to declare 3 indexed fields for event parameters. If the event parameter is less than 3 should declare all event parameters indexed

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


##

## [G-6] Lack of input value checks cause a redeployment if any human/accidental errors

> Instances(4)

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables.

If any human/accidental errors happen need to redeploy the contract so this create the huge gas lose

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
        ens = _ens;
        oracle = _oracle;
        gatewayURL = _gatewayURL;
    }

```
[OffchainDNSResolver.sol#L43-L47](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L43-L47)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

62: previousRegistrar = _previousRegistrar;
63: resolver = _resolver;
64: oracle = _dnssec;
65: suffixes = _suffixes;
67: ens = _ens;
```
[DNSRegistrar.sol#L62-L67](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L62-L67)

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

55: anchors = _anchors;

```
[DNSSECImpl.sol#L55](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L55)

##

## [G-7] Use nested if and, avoid multiple check combinations

> Instances(2)

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

As per Solidity [reports](https://gist.github.com/sathishpic22/fe96671bafb22ceaace7fc05a66bd115) possible to save 9 gas

```solidity
FILE: FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

178: if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {

```
[OffchainDNSResolver.sol#L178](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L178)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

128: if (x0 == 0 && y0 == 0) {

```
[EllipticCurve.sol#L128](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128)

##

## [G-8] Unnecessary look up in if condition

> Instances(7)

If the || condition isn’t required, the second condition will have been looked up unnecessarily.

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

86:  if (
                !rrname.equals(name) ||
                iter.class != CLASS_INET ||
                iter.dnstype != TYPE_TXT
            ) {

144:  if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {

```
[OffchainDNSResolver.sol#L86-L90](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L86-L90)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

187: if (owner == address(0) || owner == previousRegistrar) {

```
[DNSRegistrar.sol#L187](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L187)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

138: if (0 == x || x == p || 0 == y || y == p) {
392: if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {
41:  if (u == 0 || u == m || m == 0) return 0;

```
[EllipticCurve.sol#L138](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L138)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

201: if (
                    name.length != iter.data.nameLength(iter.offset) ||
                    !name.equals(0, iter.data, iter.offset, name.length)
                ) {

```
[DNSSECImpl.sol#L201-L204](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L201-L204)

##


## [G-9] Don't declare the variable inside the loops

> Instances(3)

In every iterations the new variables instance created this will consumes more gas . So just declare variables outside the loop and only use inside to save gas

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol

29: for (
            RRUtils.RRIterator memory iter = data.iterateRRs(0);
            !iter.done();
            iter.next()
        ) {
            if (iter.name().compareNames(buf.buf) != 0) continue;
            bool found;  /// @audit found inside loop
            address addr;  /// @audit addr inside loop
            (addr, found) = parseRR(data, iter.rdataOffset, iter.nextOffset);
            if (found) {
                return (addr, true);
            }
        }

51: while (idx < endIdx) {
            uint256 len = rdata.readUint8(idx);
            idx += 1;

            bool found;
            address addr;

```
[DNSClaimChecker.sol#L29-L40](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L29-L40)

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L341-L342


##

## [G-10] Functions should be used instead of modifiers to save gas

> Instances(1)

```solidity
File; 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

modifier onlyOwner() {
        Root root = Root(ens.owner(bytes32(0)));
        address owner = root.owner();
        require(msg.sender == owner);
        _;
    }

```
[DNSRegistrar.sol#L73-L78](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78)

##

## [G-11] Sort Solidity operations using short-circuit mode

> Instances(5)

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```

//f(x) is a low gas cost operation
//g(y) is a high gas cost operation
//Sort operations with different gas costs as follows
f(x) || g(y)
f(x) && g(y)

```

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

42:   if (u == 0 || u == m || m == 0) return 0;
138:  if (0 == x || x == p || 0 == y || y == p) {
392:  if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {

```
[EllipticCurve.sol#L42](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L42)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

187:  if (owner == address(0) || owner == previousRegistrar) {
```
[DNSRegistrar.sol#L187](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L187)


```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

if (
                !rrname.equals(name) ||
                iter.class != CLASS_INET ||
                iter.dnstype != TYPE_TXT
            ) {

```
[OffchainDNSResolver.sol#L86-L90](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L86-L90)



##

## [G-12] Use assembly to check for address(0)

> Instances(7)

Saves 6 gas per instance

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

102: if (dnsresolver != address(0))
197: if (resolver == address(0)) {

```
[OffchainDNSResolver.sol#L102](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L102)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

115: if (addr != address(0)) {
116: if (resolver == address(0)) {
187: if (owner == address(0) || owner == previousRegistrar) {

```
[DNSRegistrar.sol#L115-L116](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L115-L116)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

317:  if (address(algorithm) == address(0)) {
420:  if (address(digests[digesttype]) == address(0)) {

```
[DNSSECImpl.sol#L317](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L317)

##

## [G-13] Shorthand way to write if / else statement can reduce the deployment cost

> Instances(4)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

if (separator < lastIdx) {
            parentNode = textNamehash(name, separator + 1, lastIdx);
        } else {
            separator = lastIdx;
        }

```
[OffchainDNSResolver.sol#L216-L220](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L216-L220)

```solidity
FILE : 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

if (shortest - idx >= 32) {
                    mask = type(uint256).max;
                } else {
                    mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
                }

```
[BytesUtils.sol#L87-L91](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L87-L91)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

221: if (isZeroCurve(x0, y0)) {
            return (x1, y1, z1);
        } else if (isZeroCurve(x1, y1)) {
            return (x0, y0, z0);
        }

234: if (t0 == t1) {
                return twiceProj(x0, y0, z0);
            } else {
                return zeroProj();
            }

```
[EllipticCurve.sol#L221-L225](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L221-L225)

### Recommended Mitigation

```solidity

if (separator < lastIdx) {
            parentNode = textNamehash(name, separator + 1, lastIdx);
        } else {
            separator = lastIdx;
        }

```

```solidity

separator < lastIdx ? parentNode = textNamehash(name, separator + 1, lastIdx); : separator = lastIdx ;

```

##

## [G-14] The Less gas consuming condition checks should be on top

> Instances(1)

When writing conditional statements in smart contracts, it is generally best practice to order the conditions so that the less gas-consuming checks are performed first. This can help to optimize the gas usage of the contract and improve its overall efficiency

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

> "if (!isOnCurve(Q[0], Q[1])) {" check should come top then "if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) { " condition

  if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {
            // || rs[1] > lowSmax)
            return false;
        }
        if (!isOnCurve(Q[0], Q[1])) {
            return false;
        }

```
[EllipticCurve.sol#L392-L396](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L392-L396)

##

## [G-15] internal functions not called by the contract should be removed to save deployment gas

> Instances(3)

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword


```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

386: function validateSignature(
        bytes32 message,
        uint256[2] memory rs,
        uint256[2] memory Q
    ) internal pure returns (bool) {

377: function multipleGeneratorByScalar(
        uint256 scalar
    ) internal pure returns (uint256, uint256) {

316: function multiplyPowerBase2(
        uint256 x0,
        uint256 y0,
        uint256 exp
    ) internal pure returns (uint256, uint256) {

```
[EllipticCurve.sol#L386-L390](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L386-L390)

##

## [G-16] Modifiers or private functions only called once can be inlined to save gas

Instances (2)

ITs possible to save 40-50 gas

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

73: modifier onlyOwner() {

```
[DNSRegistrar.sol#L73-L78](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

273: function memcpy(uint256 dest, uint256 src, uint256 len) private pure {

```
[BytesUtils.sol#L273](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L273)

##

## [G-17] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

Instances (12)

```solidity
FILE:  2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

166: function enableNode(bytes memory domain) public returns (bytes32 node) {

174: function _enableNode(
        bytes memory domain,
        uint256 offset
    ) internal returns (bytes32 node) {

```
[DNSRegistrar.sol#L166](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L166)

```solidity
FILE: 2023-04-ens/contracts/utils/NameEncoder.sol

9: function dnsEncodeName(
        string memory name
    ) internal pure returns (bytes memory dnsName, bytes32 node) {

```
[NameEncoder.sol#L9-L11](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L9-L11)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

106: function zeroProj()
        internal
        pure
        returns (uint256 x, uint256 y, uint256 z)
    {

117:  function zeroAffine() internal pure returns (uint256 x, uint256 y) {

124: function isZeroCurve(
        uint256 x0,
        uint256 y0
    ) internal pure returns (bool isZero) {

208: function addProj(
        uint256 x0,
        uint256 y0,
        uint256 z0,
        uint256 x1,
        uint256 y1,
        uint256 z1
    ) internal pure returns (uint256 x2, uint256 y2, uint256 z2) {

335: function multiplyScalar(
        uint256 x0,
        uint256 y0,
        uint256 scalar
    ) internal pure returns (uint256 x1, uint256 y1) {

```
[EllipticCurve.sol#L106-L110](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L106-L110)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

87: function verifyRRSet(
        RRSetWithSignature[] memory input
    )
        external
        view
        virtual
        override
        returns (bytes memory rrs, uint32 inception)

107: function verifyRRSet(
        RRSetWithSignature[] memory input,
        uint256 now
    )
        public
        view
        virtual
        override
        returns (bytes memory rrs, uint32 inception)

140: function validateSignedSet(
        RRSetWithSignature memory input,
        bytes memory proof,
        uint256 now
    ) internal view returns (RRUtils.SignedSet memory rrset) {

181: function validateRRs(
        RRUtils.SignedSet memory rrset,
        uint16 typecovered
    ) internal pure returns (bytes memory name) {



```
[DNSSECImpl.sol#L87-L94](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L87-L94)

##

## [G-18] Use constants instead of type(uintx).max

Instances (5):

type(uint256).max it uses more gas in the distribution process and also for each transaction than constant usage

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/RecordParser.sol

24: if (separator == type(uint256).max) {
25: return ("", "", type(uint256).max);

33: if (terminator == type(uint256).max) {

```
[RecordParser.sol#L24-L25](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L24-L25)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

398: return type(uint256).max;
88:  mask = type(uint256).max;

```
[BytesUtils.sol#L398](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L398)

##

## [G-19] Instead of calculating bytes32(0) every time inside the contract its possible to use constants to reduce the gas cost

Instances (1)

By defining a constant variable, you can avoid computing the value of bytes32(0) every time it is used in your contract. Instead, you can simply reference the constant variable, which will have the same value as bytes32(0)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

74:  Root root = Root(ens.owner(bytes32(0)));
180: return bytes32(0);
188: if (parentNode == bytes32(0)) {
189: Root root = Root(ens.owner(bytes32(0)));

```
[DNSRegistrar.sol#L74](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L74)

##

## [G-20] Structs can be packed into fewer storage slots

> Instances (1)

> Aproximate Gas Saved: 40000 gas (2 Slots)

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas

For offset,rdataOffset,nextOffset variables uint128 alone enough. So we can avoid 2 slots (40000 gas )

```solidity
2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

  120: struct RRIterator {
  121:        bytes data;
+ 122:        uint128 offset;
- 122:        uint256 offset;
  123:        uint16 dnstype;
  124:        uint16 class;
  125:        uint32 ttl;
+ 126:        uint128 rdataOffset;
+ 127:        uint128 nextOffset;
- 126:        uint256 rdataOffset;
- 127:        uint256 nextOffset;
  128:    }

```


[G‑01]	Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate	1	-
[G‑02]	State variables should be cached in stack variables rather than re-reading them from storage	15	1455
[G‑03]	Multiple accesses of a mapping/array should use a local variable cache	11	462
[G‑04]	<x> += <y> costs more gas than <x> = <x> + <y> for state variables	5	565
[G‑05]	internal functions only called once can be inlined to save gas	4	80
[G‑06]	Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement	5	425
[G‑07]	<array>.length should not be looked up in every loop of a for-loop	4	12
[G‑08]	++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops	8	480
[G‑09]	require()/revert() strings longer than 32 bytes cost extra gas	86	-
[G‑10]	Optimize names to save gas	19	418
[G‑11]	Using bools for storage incurs overhead	4	68400
[G‑12]	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	4	20
[G‑13]	Splitting require() statements that use && saves gas	1	3
[G‑14]	Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead	4	-
[G‑15]	Using private rather than public for constants, saves gas	6	-
[G‑16]	Division by two should use bit shifting	2	40
[G‑17]	require() or revert() statements that check input arguments should be at the top of the function	1	-
[G‑18]	Use custom errors rather than revert()/require() strings to save gas	88	-
[G‑19]	Functions guaranteed to revert when called by normal users can be marked payable	36	756
[G‑20]	Constructors can be marked payable	7	147