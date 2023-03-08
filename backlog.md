# Outline

- Present the article
- Explain how it works in ERC20
  - Run Fuzz test
  - Show effect of isMintableOrBurnable
  - Show why multi-abi is important by testing external harness with internal config (no multi-abi) on `isMintableOrBurnable=false` (false negative).
- ERC4626
  - deposit, mint, redeem, withdraw
  - Explain structure
  - Open FunctionalAccountingProps to explain first test
  - Hint: `try vault.deposit(tokens,receiver)` ensures there's no DoS. 
  - Fuzz with no changes. 
  - Play with ERC4626::deposit and put `equire((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");` after `_mint` then do fuzz.
- Math
  - Run and show the error failure
  - Show Issue in the test itself that can cause a false negative
    - This assert `assert(result <= MAX_64x64 && result >= MIN_64x64);` always passes because `result` is `int128`, therefore the test is not actually valid.
    - Therefore, note that we use these properties with care.
    - Add this to `test_add_range`:
```java
assert( !(x >= 0 && y >= 0)  || (result >= x && result >= y));
assert(!(x >= 0 && y < 0)  || (result < x && result >= y));
assert(!(x < 0 && y >= 0)  || (result < y && result >= x));
```

# Notes
- I am not showing anything new but I am showing a way of organization

# Commands
- Fuzz to test ABDKMath64x64
```
echidna-test .  --contract CryticABDKMath64x64Harness  --test-mode assertion  --corpus-dir echidna-corpus --seq-len 1 --test-limit 10000
```