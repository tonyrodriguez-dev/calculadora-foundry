# Calculadora Foundry

A small calculator smart contract built and tested with [Foundry](https://book.getfoundry.sh/), written while following the Blockchain Accelerator course.

## What it shows

- **Contract** (`src/Calculadora.sol`): addition, subtraction, multiplication and division. Each operation stores the last result and emits an event. `division` is restricted to an admin with an `onlyAdmin` modifier.
- **Unit tests** (`test/CalculadoraTest.t.sol`): happy paths, expected reverts (multiplication overflow, non-admin caller), division by zero, and `setUp`-based deployment.
- **Cheatcodes**: `vm.addr`, `vm.startPrank` / `vm.stopPrank`, `vm.expectRevert`.
- **Fuzz test**: `testFuzzingDivision` with random inputs (350 runs, set in `foundry.toml`).
- **Coverage**: 100% of lines, statements, branches and functions with `forge coverage`.

## Usage

```shell
git clone --recurse-submodules <this-repo-url>
cd calculadora-foundry
forge build
forge test
forge coverage
```

Run a single test with traces:

```shell
forge test --match-test testAddition -vvvv
```

## Testing

The suite has **11 tests** (10 unit tests and 1 fuzz test) and reaches **100% coverage** (lines, statements, branches and functions).

**Unit tests** use fixed inputs and check an exact outcome. Some check that things work, others check that they fail when they should:

| Test | What it checks |
| --- | --- |
| `testCheckFirstResultado` | The constructor stores the initial result (100) |
| `testAddition`, `testSubstraction`, `testMultiplier` | Each operation returns the right value |
| `testCanNotMultiply2LargeNumbers` | Multiplication overflow reverts |
| `testIfNotAdminCallsDivisionReverts` | A random user impersonated with `vm.startPrank` cannot divide |
| `testDefaultCanNotCallDivisionCorrectly` | The default test sender is not the admin, so it reverts |
| `testAdminCanCallDivisionCorrectly` | The admin can divide |
| `testDefaultExecutesCorrectly` | Division returns the expected result (integer division rounds down) |
| `testDivideByZeroReturnsZero` | Dividing by zero returns 0 instead of reverting |

**Fuzz test**: `testFuzzingDivision` calls `division` with random `uint256` inputs (350 runs, configured in `foundry.toml`) to look for edge cases that fixed inputs can miss, such as a zero divisor.

**Unit vs fuzz**: a unit test uses inputs you choose; a fuzz test lets Foundry generate many random inputs and runs the same checks on each of them.

## Notes

- Solidity `0.8.34`; Foundry installs the compiler automatically.
- Division by zero returns `0` instead of reverting, and the tests document that behaviour.

## License

MIT
