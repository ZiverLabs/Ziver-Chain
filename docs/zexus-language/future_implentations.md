# Future implementations & roadmap

Date: 2025-11-08

This document captures the remaining issues observed after recent parser fixes, concrete remediation steps, suggested improvements to make the project more robust, and a prioritized roadmap with estimated effort. Additions here should be actionable; each item lists targeted files/areas to edit, tests to add, and a short validation checklist.

## Current minor issues (observed)

1. Empty expression parsing (debug-only)
   - Symptom: parsing logs show empty-expression runs like:
     - "Parsing expression from tokens: ['']" followed by "Parsed 1 statements from block"
   - Impact: low (cosmetic) — creates extraneous BlockStatement entries and noisy debug logs.
   - Likely cause: the structural analyzer or context parser is creating a top-level block with an empty token ('' literal) and the context parser treats it as a statement instead of skipping empty tokens.
   - Fix (concrete):
     - In `src/zexus/strategy_structural.py`: ensure token sequences used for block `tokens` filter out empty-literal tokens (token.literal == '' or token.type==EOF) before handing to `ContextStackParser`.
     - In `src/zexus/strategy_context.py`: when parsing a block, early-return if the block's token list is empty or contains only whitespace/empty tokens.
   - Tests to add:
     - Unit test: create a token sequence with an explicit empty token block and assert the context parser returns None or an empty BlockStatement with 0 statements.
     - Integration: run the focused parsing test with debug logging enabled and assert no "Parsing expression from tokens: ['']" lines appear.

2. Method chain fragmentation
   - Symptom: long chained call expressions (e.g., `numbers.map(...).filter(...).reduce(...)`) may be split across multiple structural blocks; context parser then parses them in parts.
   - Impact: medium — doesn't always change semantics but can complicate transformations and analysis and may affect evaluation ordering if blocks are handled separately.
   - Likely cause: `strategy_structural.py` splitting heuristics divide token streams on punctuation or identifiers that occur mid-chain (for example, splitting at '.' tokens into separate blocks).
   - Fix (concrete):
     - Adjust `strategy_structural.py` to treat '.' and `IDENT` sequences that are adjacent to `LPAREN`/`RPAREN` groups as part of the same statement when they form obvious call-chains.
     - Implement a simple chain-merging pass: when a block ends with a chain-terminator (like '.' or an open-call that continues) and the next block begins with a continuation token (IDENT, DOT), merge the adjacent blocks before parsing.
   - Tests to add:
     - Integration test: parse a long chain and assert the AST is a single nested `MethodCallExpression` rather than several partial statements.
     - Regression: ensure small statements are still split correctly (avoid over-merging).

3. List element counting (debug output)
   - Symptom: debug output reports off-by-one element counts for list literals (e.g., "Parsed list with 3 elements" when the input had two elements).
   - Impact: low — appears to be an off-by-one in the debug counter, not necessarily an AST bug.
   - Likely cause: debug print occurs after an extra `parse_expression` or repeats the last element when logging, or an index/count mismatch in `parse_expression_list`.
   - Fix (concrete):
     - Review `parse_expression_list` and `parse_list_literal` implementations in `src/zexus/parser.py` and `src/zexus/strategy_context.py`.
     - Ensure elements are appended exactly once and debug print uses `len(elements)` after parsing completes.
   - Tests to add:
     - Unit test: parse `[1,2]` and assert `ListLiteral.elements` length == 2.
     - Integration: run the focused tests and assert debug message uses the correct number (or replace debug prints with logging and assert the correct count in logs).

4. Property chain truncation (foo.bar.baz().qux)
   - Symptom: parsing sometimes stops mid-chain (e.g., `foo.bar.` parsed separately then `baz()` in another block) causing extra block boundaries.
   - Impact: medium — same as chain fragmentation; parsing works but is less efficient.
   - Fix (concrete): same as Method chain fragmentation above: structural analyzer must keep dotted chains together. Additionally, `ContextStackParser` should apply a robust primary+chaining approach (parse a primary, then loop to consume dot/call tokens until none remain) — ensure the tolerant parser's chaining loop checks peek tokens correctly and doesn't stop early.
   - Tests to add:
     - Unit/integration: parse `foo.bar.baz().qux` and assert a single nested expression representing the property access on the final call.

## Immediate actionable plan (short term)

Priority 1 (High, 1-2 days):
- Stop emitting empty-expression debug lines.
  - Files: `src/zexus/strategy_structural.py`, `src/zexus/strategy_context.py`.
  - Tasks:
    1. Filter empty tokens in structural analyzer when building blocks (avoid token.literal == '' entries).
    2. In context parser, early-return when a block's token list is empty or all tokens are whitespace/EOF.
  - Tests:
    - Add unit test for empty-block handling.

Priority 2 (Medium, 1-3 days):
- Merge chain fragments and ensure chained expressions are parsed as single AST nodes.
  - Files: `src/zexus/strategy_structural.py`, `src/zexus/strategy_context.py`.
  - Tasks:
    1. Implement chain-merging pass in the structural analyzer for adjacent blocks.
    2. Harden the context parser chaining loop (primary then while-loop consuming DOT/LPAREN tokens).
    3. Add tests for long method chains and property chains.

Priority 3 (Low, 1-2 days):
- Fix list element counting and tidy debug logs to use logging with levels.
  - Files: `src/zexus/parser.py`, `src/zexus/strategy_context.py`.
  - Tasks:
    1. Audit `parse_expression_list` and list parsing debug statements.
    2. Replace print/debug statements with `logging` module calls behind a debug flag.
    3. Add tests for correct ListLiteral element counts.

## Broader suggestions to make the project stronger

1. Convert debug prints to proper Python logging
   - Replace ad-hoc prints with uses of the `logging` module and configure levels via config. This makes enabling/disabling verbose output trivial in CI and local dev.

2. Expand test coverage and continuous integration
   - Convert the focused test script into pytest unit tests (done-in-progress) and add them to `tests/`.
   - Add a GitHub Actions workflow to run the full pytest matrix with `PYTHONPATH=./src` and/or `pip install -e .` to ensure imports succeed.

3. Centralize lambda parsing helpers
   - There's duplication between `src/zexus/parser.py` and `src/zexus/strategy_context.py` in lambda parsing logic. Create a shared helper module (e.g., `src/zexus/lambda_helpers.py`) exposing small utilities:
     - normalize_parameter_list(token_list) -> [Identifier]
     - parse_lambda_body(parser) -> Expression
   - This reduces drift and makes future syntax variants easier to add.

4. Structural analyzer improvements and semantic hints
   - Consider enriching structural analyzer output with small hints for context parser (e.g., `ends_with_dot`, `contains_lambda`, `is_map_literal`) so the context parser can make local parsing decisions faster and with fewer scans.

5. Add AST validation and roundtrip tests
   - Implement an AST validator that asserts invariants (e.g., ListLiteral.elements is list, MapLiteral.pairs is list of 2-tuples with Identifier/String keys, `MethodCallExpression.object` is an Expression, etc.).
   - Add roundtrip tests: parse -> AST -> pretty-print -> parse again -> ensure AST equivalence for key constructs.

6. Fuzz testing and property-based tests
   - Use Hypothesis or a lightweight fuzz harness to randomly generate token sequences (or mutated real examples) and ensure parsers don't throw unhandled exceptions and either produce an AST or return controlled errors.

7. Performance and profiling
   - Add microbenchmarks for the lexer/parser for large files (thousands of tokens) and profile hot paths.
   - Optimize structural analyzer passes that do repeated string scanning; consider token-index-based operations rather than string lookahead for heavy-duty parsing.

8. Developer docs and migration notes
   - Add `docs/developer_guide.md` with common commands, how to run tests, how to enable debug logs, and how to add parser tests.

## Recommended quick tasks (next 48 hours)

1. Implement the empty-token filter in `strategy_structural.py` and early-return in `strategy_context.py` (priority=high). Validate with the focused test script and re-run pytest.
2. Convert the focused test script to proper pytest tests (finish current in-progress todo). Add the test to CI.
3. Replace a sample of debug prints with `logging` and run with DEBUG level to confirm the approach.

## How to run tests locally (copyable)

```bash
# Run focused tests (single file)
PYTHONPATH=./src pytest tests/test_lambda_and_chains_pytest.py -q

# Run the full test suite
PYTHONPATH=./src pytest -q

# Run a single test in verbose/debug mode (enable debug logs in config or set env var if supported)
PYTHONPATH=./src pytest tests/test_lambda_and_chains_pytest.py::test_parsing_cases -q -k arrow
```

## Closing notes

The parser is in an excellent state; the remaining issues are primarily structural or cosmetic and can be eliminated with a few targeted edits and tests. If you want, I can:

- Implement the empty-token filtering and re-run the tests now (recommended immediate fix).
- Finish the pytest conversion and add a CI workflow that runs the tests automatically.

Tell me which of the quick tasks above you'd like me to pick up next and I'll implement it and update the `todo` accordingly.
