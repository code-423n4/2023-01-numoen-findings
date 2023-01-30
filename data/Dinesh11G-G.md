### 1st BUG
Don't Initialize Variables with Default Value

#### Impact
Issue Information: 
Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
2023-01-numoen/lib/forge-std/src/StdStorage.sol::66 => for (uint256 i = 0; i < reads.length; i++) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::175 => for (uint256 i = 0; i < max; i++) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::183 => for (uint256 i = 0; i < b.length; i++) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::308 => for (uint256 i = 0; i < max; i++) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::317 => for (uint256 i = 0; i < b.length; i++) {
```
#### Tools used
Manual VS Code audit

### 2nd BUG
Cache Array Length Outside of Loop

#### Impact
Issue Information: 
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2023-01-numoen/lib/forge-std/::445 => if (a.length == b.length) {
2023-01-numoen/lib/forge-std/::446 => for (uint i = 0; i < a.length; i++) {
2023-01-numoen/lib/forge-std/src/StdChains.sol::63 => require(bytes(chainAlias).length != 0, "StdChains getChain(string): Chain alias cannot be the empty string.");
2023-01-numoen/lib/forge-std/src/StdChains.sol::93 => bytes(chainAlias).length != 0, "StdChains setChain(string,Chain): Chain alias cannot be the empty string."
2023-01-numoen/lib/forge-std/src/StdChains.sol::102 => bytes(foundAlias).length == 0 || keccak256(bytes(foundAlias)) == keccak256(bytes(chainAlias)),
2023-01-numoen/lib/forge-std/src/StdChains.sol::128 => if (bytes(chain.rpcUrl).length == 0) {
2023-01-numoen/lib/forge-std/src/StdChains.sol::136 => if (keccak256(notFoundError) != keccak256(err) || bytes(chain.rpcUrl).length == 0) {
2023-01-numoen/lib/forge-std/src/StdCheats.sol::244 => Tx1559[] memory txs = new Tx1559[](rawTxs.length);
2023-01-numoen/lib/forge-std/src/StdCheats.sol::245 => for (uint256 i; i < rawTxs.length; i++) {
2023-01-numoen/lib/forge-std/src/StdCheats.sol::312 => Receipt[] memory receipts = new Receipt[](rawReceipts.length);
2023-01-numoen/lib/forge-std/src/StdCheats.sol::313 => for (uint256 i; i < rawReceipts.length; i++) {
2023-01-numoen/lib/forge-std/src/StdCheats.sol::343 => ReceiptLog[] memory logs = new ReceiptLog[](rawLogs.length);
2023-01-numoen/lib/forge-std/src/StdCheats.sol::344 => for (uint256 i; i < rawLogs.length; i++) {
2023-01-numoen/lib/forge-std/src/StdCheats.sol::424 => require(b.length <= 32, "StdCheats _bytesToUint(bytes): Bytes length exceeds 32.");
2023-01-numoen/lib/forge-std/src/StdCheats.sol::425 => return abi.decode(abi.encodePacked(new bytes(32 - b.length), b), (uint256));
2023-01-numoen/lib/forge-std/src/StdStorage.sol::51 => if (reads.length == 1) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::65 => } else if (reads.length > 1) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::66 => for (uint256 i = 0; i < reads.length; i++) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::174 => uint256 max = b.length > 32 ? 32 : b.length;
2023-01-numoen/lib/forge-std/src/StdStorage.sol::182 => bytes memory result = new bytes(b.length * 32);
2023-01-numoen/lib/forge-std/src/StdStorage.sol::183 => for (uint256 i = 0; i < b.length; i++) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::307 => uint256 max = b.length > 32 ? 32 : b.length;
2023-01-numoen/lib/forge-std/src/StdStorage.sol::316 => bytes memory result = new bytes(b.length * 32);
2023-01-numoen/lib/forge-std/src/StdStorage.sol::317 => for (uint256 i = 0; i < b.length; i++) {
2023-01-numoen/lib/forge-std/src/StdUtils.sol::104 => require(b.length <= 32, "StdUtils bytesToUint(bytes): Bytes length exceeds 32.");
2023-01-numoen/lib/forge-std/src/StdUtils.sol::105 => return abi.decode(abi.encodePacked(new bytes(32 - b.length), b), (uint256));
2023-01-numoen/lib/forge-std/src/Vm.sol::27 => uint256 length;
2023-01-numoen/lib/forge-std/src/console.sol::8 => uint256 payloadLength = payload.length;
2023-01-numoen/lib/forge-std/src/console2.sol::13 => uint256 payloadLength = payload.length;
```
#### Tools used
Manual VS Code audit

### 3rd BUG
Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: 
When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");

#### Findings:
```
2023-01-numoen/lib/forge-std/::83 => return hevmCodeSize > 0;
2023-01-numoen/lib/forge-std/src/StdMath.sol::13 => return uint256(a > 0 ? a : -a);
```
#### Tools used
Manual VS Code audit

### 4th BUG
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
2023-01-numoen/lib/forge-std/::42 => address(bytes20(uint160(uint256(keccak256('hevm cheat code')))));
2023-01-numoen/lib/forge-std/::55 => bytes4(keccak256("load(address,bytes32)")),
2023-01-numoen/lib/forge-std/::69 => bytes4(keccak256("store(address,bytes32,bytes32)")),
2023-01-numoen/lib/forge-std/::429 => if (keccak256(abi.encodePacked(a)) != keccak256(abi.encodePacked(b))) {
2023-01-numoen/lib/forge-std/::437 => if (keccak256(abi.encodePacked(a)) != keccak256(abi.encodePacked(b))) {
2023-01-numoen/lib/forge-std/src/Base.sol::9 => address internal constant VM_ADDRESS = address(uint160(uint256(keccak256("hevm cheat code"))));
2023-01-numoen/lib/forge-std/src/Base.sol::13 => address internal constant DEFAULT_SENDER = address(uint160(uint256(keccak256("foundry default caller"))));
2023-01-numoen/lib/forge-std/src/StdAssertions.sol::53 => if (keccak256(abi.encode(a)) != keccak256(abi.encode(b))) {
2023-01-numoen/lib/forge-std/src/StdAssertions.sol::62 => if (keccak256(abi.encode(a)) != keccak256(abi.encode(b))) {
2023-01-numoen/lib/forge-std/src/StdAssertions.sol::71 => if (keccak256(abi.encode(a)) != keccak256(abi.encode(b))) {
2023-01-numoen/lib/forge-std/src/StdAssertions.sol::80 => if (keccak256(abi.encode(a)) != keccak256(abi.encode(b))) {
2023-01-numoen/lib/forge-std/src/StdAssertions.sol::87 => if (keccak256(abi.encode(a)) != keccak256(abi.encode(b))) {
2023-01-numoen/lib/forge-std/src/StdAssertions.sol::94 => if (keccak256(abi.encode(a)) != keccak256(abi.encode(b))) {
2023-01-numoen/lib/forge-std/src/StdChains.sol::38 => VmSafe private constant vm = VmSafe(address(uint160(uint256(keccak256("hevm cheat code")))));
2023-01-numoen/lib/forge-std/src/StdChains.sol::102 => bytes(foundAlias).length == 0 || keccak256(bytes(foundAlias)) == keccak256(bytes(chainAlias)),
2023-01-numoen/lib/forge-std/src/StdChains.sol::136 => if (keccak256(notFoundError) != keccak256(err) || bytes(chain.rpcUrl).length == 0) {
2023-01-numoen/lib/forge-std/src/StdCheats.sol::10 => Vm private constant vm = Vm(address(uint160(uint256(keccak256("hevm cheat code")))));
2023-01-numoen/lib/forge-std/src/StdCheats.sol::404 => privateKey = uint256(keccak256(abi.encodePacked(name)));
2023-01-numoen/lib/forge-std/src/StdCheats.sol::473 => Vm private constant vm = Vm(address(uint160(uint256(keccak256("hevm cheat code")))));
2023-01-numoen/lib/forge-std/src/StdJson.sol::30 => VmSafe private constant vm = VmSafe(address(uint160(uint256(keccak256("hevm cheat code")))));
2023-01-numoen/lib/forge-std/src/StdStorage.sol::20 => Vm private constant vm = Vm(address(uint160(uint256(keccak256("hevm cheat code")))));
2023-01-numoen/lib/forge-std/src/StdStorage.sol::23 => return bytes4(keccak256(bytes(sigStr)));
2023-01-numoen/lib/forge-std/src/StdStorage.sol::39 => if (self.finds[who][fsig][keccak256(abi.encodePacked(ins, field_depth))]) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::40 => return self.slots[who][fsig][keccak256(abi.encodePacked(ins, field_depth))];
2023-01-numoen/lib/forge-std/src/StdStorage.sol::62 => emit SlotFound(who, fsig, keccak256(abi.encodePacked(ins, field_depth)), uint256(reads[0]));
2023-01-numoen/lib/forge-std/src/StdStorage.sol::63 => self.slots[who][fsig][keccak256(abi.encodePacked(ins, field_depth))] = uint256(reads[0]);
2023-01-numoen/lib/forge-std/src/StdStorage.sol::64 => self.finds[who][fsig][keccak256(abi.encodePacked(ins, field_depth))] = true;
2023-01-numoen/lib/forge-std/src/StdStorage.sol::82 => emit SlotFound(who, fsig, keccak256(abi.encodePacked(ins, field_depth)), uint256(reads[i]));
2023-01-numoen/lib/forge-std/src/StdStorage.sol::83 => self.slots[who][fsig][keccak256(abi.encodePacked(ins, field_depth))] = uint256(reads[i]);
2023-01-numoen/lib/forge-std/src/StdStorage.sol::84 => self.finds[who][fsig][keccak256(abi.encodePacked(ins, field_depth))] = true;
2023-01-numoen/lib/forge-std/src/StdStorage.sol::95 => self.finds[who][fsig][keccak256(abi.encodePacked(ins, field_depth))],
2023-01-numoen/lib/forge-std/src/StdStorage.sol::104 => return self.slots[who][fsig][keccak256(abi.encodePacked(ins, field_depth))];
2023-01-numoen/lib/forge-std/src/StdStorage.sol::196 => Vm private constant vm = Vm(address(uint160(uint256(keccak256("hevm cheat code")))));
2023-01-numoen/lib/forge-std/src/StdStorage.sol::258 => if (!self.finds[who][fsig][keccak256(abi.encodePacked(ins, field_depth))]) {
2023-01-numoen/lib/forge-std/src/StdStorage.sol::261 => bytes32 slot = bytes32(self.slots[who][fsig][keccak256(abi.encodePacked(ins, field_depth))]);
2023-01-numoen/lib/forge-std/src/StdUtils.sol::8 => VmSafe private constant vm = VmSafe(address(uint160(uint256(keccak256("hevm cheat code")))));
2023-01-numoen/lib/forge-std/src/StdUtils.sol::75 => if (nonce == 0x00)      return addressFromLast20Bytes(keccak256(abi.encodePacked(bytes1(0xd6), bytes1(0x94), deployer, bytes1(0x80))));
2023-01-numoen/lib/forge-std/src/StdUtils.sol::76 => if (nonce <= 0x7f)      return addressFromLast20Bytes(keccak256(abi.encodePacked(bytes1(0xd6), bytes1(0x94), deployer, uint8(nonce))));
2023-01-numoen/lib/forge-std/src/StdUtils.sol::79 => if (nonce <= 2**8 - 1)  return addressFromLast20Bytes(keccak256(abi.encodePacked(bytes1(0xd7), bytes1(0x94), deployer, bytes1(0x81), uint8(nonce))));
2023-01-numoen/lib/forge-std/src/StdUtils.sol::80 => if (nonce <= 2**16 - 1) return addressFromLast20Bytes(keccak256(abi.encodePacked(bytes1(0xd8), bytes1(0x94), deployer, bytes1(0x82), uint16(nonce))));
2023-01-numoen/lib/forge-std/src/StdUtils.sol::81 => if (nonce <= 2**24 - 1) return addressFromLast20Bytes(keccak256(abi.encodePacked(bytes1(0xd9), bytes1(0x94), deployer, bytes1(0x83), uint24(nonce))));
2023-01-numoen/lib/forge-std/src/StdUtils.sol::90 => keccak256(abi.encodePacked(bytes1(0xda), bytes1(0x94), deployer, bytes1(0x84), uint32(nonce)))
2023-01-numoen/lib/forge-std/src/StdUtils.sol::100 => return addressFromLast20Bytes(keccak256(abi.encodePacked(bytes1(0xff), deployer, salt, initcodeHash)));
2023-01-numoen/src/core/Factory.sol::82 => lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
2023-01-numoen/src/libraries/Balance.sol::14 => token.staticcall(abi.encodeWithSelector(bytes4(keccak256(bytes("balanceOf(address)"))), address(this)));
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::22 => keccak256(
2023-01-numoen/src/periphery/libraries/LendgineAddress.sol::24 => keccak256(
2023-01-numoen/src/periphery/libraries/LendgineAddress.sol::28 => keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)),
```
#### Tools used
Manual VS Code audit

### 5th BUG
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2023-01-numoen/lib/forge-std/src/StdChains.sol::63 => require(bytes(chainAlias).length != 0, "StdChains getChain(string): Chain alias cannot be the empty string.");
2023-01-numoen/lib/forge-std/src/StdChains.sol::178 => setChainWithDefaultRpcUrl("gnosis_chain", Chain("Gnosis Chain", 100, "https://rpc.gnosischain.com"));
2023-01-numoen/lib/forge-std/src/StdCheats.sol::289 => string memory key = string(abi.encodePacked(".transactions[", vm.toString(index), "]"));
2023-01-numoen/lib/forge-std/src/StdCheats.sol::305 => string memory key = string(abi.encodePacked(".receipts[", vm.toString(index), "]"));
2023-01-numoen/lib/forge-std/src/StdCheats.sol::368 => require(addr != address(0), "StdCheats deployCode(string,bytes): Deployment failed.");
2023-01-numoen/lib/forge-std/src/StdCheats.sol::378 => require(addr != address(0), "StdCheats deployCode(string): Deployment failed.");
2023-01-numoen/lib/forge-std/src/StdCheats.sol::389 => require(addr != address(0), "StdCheats deployCode(string,bytes,uint256): Deployment failed.");
2023-01-numoen/lib/forge-std/src/StdCheats.sol::399 => require(addr != address(0), "StdCheats deployCode(string,uint256): Deployment failed.");
2023-01-numoen/lib/forge-std/src/StdCheats.sol::424 => require(b.length <= 32, "StdCheats _bytesToUint(bytes): Bytes length exceeds 32.");
2023-01-numoen/lib/forge-std/src/StdStorage.sol::59 => "stdStorage find(StdStorage): Packed slot. This would cause dangerous overwriting and currently isn't supported."
2023-01-numoen/lib/forge-std/src/StdStorage.sol::91 => require(false, "stdStorage find(StdStorage): No storage use detected for target.");
2023-01-numoen/lib/forge-std/src/StdStorage.sol::96 => "stdStorage find(StdStorage): Slot(s) not found."
2023-01-numoen/lib/forge-std/src/StdStorage.sol::156 => revert("stdStorage read_bool(StdStorage): Cannot decode. Make sure you are reading a bool.");
2023-01-numoen/lib/forge-std/src/StdStorage.sol::273 => "stdStorage find(StdStorage): Packed slot. This would cause dangerous overwriting and currently isn't supported."
2023-01-numoen/lib/forge-std/src/StdUtils.sol::17 => require(min <= max, "StdUtils bound(uint256,uint256,uint256): Max is less than min.");
2023-01-numoen/lib/forge-std/src/StdUtils.sol::49 => require(min <= max, "StdUtils bound(int256,int256,int256): Max is less than min.");
2023-01-numoen/lib/forge-std/src/StdUtils.sol::104 => require(b.length <= 32, "StdUtils bytesToUint(bytes): Bytes length exceeds 32.");
```
#### Tools used
Manual VS Code audit

### 6th BUG
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: 
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2023-01-numoen/lib/forge-std/src/StdStorage.sol::176 => out |= bytes32(b[offset + i] & 0xFF) >> (i * 8);
2023-01-numoen/lib/forge-std/src/StdStorage.sol::309 => out |= bytes32(b[offset + i] & 0xFF) >> (i * 8);
2023-01-numoen/lib/forge-std/src/StdUtils.sol::79 => if (nonce <= 2**8 - 1)  return addressFromLast20Bytes(keccak256(abi.encodePacked(bytes1(0xd7), bytes1(0x94), deployer, bytes1(0x81), uint8(nonce))));
2023-01-numoen/lib/forge-std/src/StdUtils.sol::81 => if (nonce <= 2**24 - 1) return addressFromLast20Bytes(keccak256(abi.encodePacked(bytes1(0xd9), bytes1(0x94), deployer, bytes1(0x83), uint24(nonce))));
```
#### Tools used
Manual VS Code audit