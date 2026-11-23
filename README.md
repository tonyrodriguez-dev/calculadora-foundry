# Calculadora Foundry

Calculadora is a calculator smart contract built with Solidity 0.8.34 and tested with [Foundry](https://book.getfoundry.sh/). It stores the result of the last operation and emits an event for every one of them. Addition, subtraction and multiplication are open to anyone; division can only be called by the administrator set at deployment.

The focus of the project is testing: 10 unit tests, 1 fuzz test and 100% coverage.

## Concepts practiced

- Foundry project setup: `forge build`, `forge test`, `forge coverage`
- State variables, events, constructor and an `onlyAdmin` access-control modifier
- Named return values and integer arithmetic (overflow reverts, division rounds down)
- Test contracts that inherit from `Test`, with `setUp` for deployment
- Cheatcodes: `vm.addr`, `vm.startPrank` / `vm.stopPrank`, `vm.expectRevert`
- Unit testing vs fuzz testing, configured in `foundry.toml`
- Reading test traces with `-vvvv`

## How it works

| Function | Behavior |
| --- | --- |
| `addition(a_, b_)` | Returns `a_ + b_`, stores it in `resultado` and emits `Addition`. |
| `substraction(a_, b_)` | Returns `a_ - b_`. Reverts on underflow. Emits `Substraction`. |
| `multiplier(a_, b_)` | Returns `a_ * b_`. Reverts on overflow. Emits `Multiplier`. |
| `division(a_, b_)` | Admin only. Returns `a_ / b_` rounded down, or `0` when `b_` is `0`. Emits `Division` on success. |
| `resultado()` / `admin()` | Return the last result and the administrator's address. |

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

## Run locally

```shell
git clone --recurse-submodules https://github.com/tonyrodriguez-dev/calculadora-foundry.git
cd calculadora-foundry
forge build
forge test
forge coverage
```

Run a single test with traces:

```shell
forge test --match-test testAddition -vvvv
```

Foundry installs Solidity `0.8.34` automatically.

## Project structure

```text
src/Calculadora.sol            contract
test/CalculadoraTest.t.sol     unit and fuzz tests
foundry.toml                   compiler and fuzz settings (350 runs)
.github/workflows/test.yml     CI: fmt check, build and tests
```

Calculadora is an educational project. It has not been audited.

## Credits

Contract and tests written in class for the Blockchain Accelerator course taught by José; this repository holds my copy of the exercise with added documentation.

## License

MIT
