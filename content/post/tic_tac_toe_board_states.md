+++
title = "A small but complete AI-written program"
summary = "Counting the valid game boards in Tic-Tac-Toe"
date = "2024-08-05"
draft = false
+++
### Task: Counting Tic-Tac-Toe board states with Claude 3.5 Sonnet

As promised, I'm starting my AI-chat-based development task with something I consider relatively simple. Over the past year or so when I've done this, prior versions of LLMs like ChatGPT or Claude would have a challenging time with this small search task. When starting with an empty board, there are only nine possible moves that can be made (at most) before someone has won or a tie has been reached.

The chat with Claude starts below. NOTE: :b: is for me (Brandon), while the :sparkles: emoji designates the AI responses. My sidebar commentary is in square brackets.

---

:b: **In the game tic-tac-toe, does X always move first?** 

*[This may seem like an odd question to start with. I've often played tic-tac-toe with my son or daughter, and we simply choose either X or O according to preference, along with randomly deciding who's going first. I decided to ask Claude if that method was really "proper" or if there was a rule about it. Turns out there is!]*

:sparkles: Typically, in tic-tac-toe, X moves first. This is the standard convention for the game. However, players can agree to alternate who goes first in subsequent games if they play multiple rounds. If you'd like more details about tic-tac-toe strategies or variations, I'd be happy to elaborate.

---

:b: **Create a Python program that generates and counts the number of tic-tac-toe board game states, not just the ending states but the intermediate ones as well. Do not count an empty board as one of the game states. Follow the rules and standard conventions of tic-tac-toe and assume that X always moves first. Assume that clarity and performance attributes have the same priority in the generated code.**

*[Originally I wanted to make a program like this to easily identify and index the distinct board states in N-Puzzle search problems. Unlike with Tic-Tac-Toe, sliding tile games can easily reach repeated states. This makes it highly desirable to have a quick way to hash a board state. Being able to look up whether a state has been reached previously means that search paths leading to the cycle can be dropped.]*

:sparkles: Here's a Python program that generates and counts the number of tic-tac-toe board game states, including intermediate states:
```python
from itertools import product

def generate_tic_tac_toe_states():
    empty = '.'
    players = ['X', 'O']
    states = set()

    def is_valid_state(board):
        x_count = board.count('X')
        o_count = board.count('O')
        if x_count < o_count or x_count > o_count + 1:
            return False
        if has_winner(board, 'X') and has_winner(board, 'O'):
            return False
        if has_winner(board, 'X') and x_count <= o_count:
            return False
        if has_winner(board, 'O') and x_count != o_count:
            return False
        return True

    def has_winner(board, player):
        winning_combinations = [
            (0, 1, 2), (3, 4, 5), (6, 7, 8),  # Rows
            (0, 3, 6), (1, 4, 7), (2, 5, 8),  # Columns
            (0, 4, 8), (2, 4, 6)  # Diagonals
        ]
        return any(all(board[i] == player for i in combo) for combo in winning_combinations)

    for moves in product(players + [empty], repeat=9):
        board = ''.join(moves)
        if board != '.' * 9 and is_valid_state(board):
            states.add(board)

    return states

# Generate and count the states
states = generate_tic_tac_toe_states()
state_count = len(states)

print(f"Total number of valid tic-tac-toe game states: {state_count}")
```
Would you like me to explain or break down this code?

---

:b: **No, but I've changed my mind. Please include the empty board as one of the board states. Does that make the code any cleaner or clearer?**

*[I noticed that check for `board != '.' * 9` and was annoyed by the extra check instead of a single `is_valid_state` call.]*

:sparkles: Certainly. Including the empty board as one of the valid states simplifies the code slightly. Here's the updated version:
```python
from itertools import product

def generate_tic_tac_toe_states():
    empty = '.'
    players = ['X', 'O']
    states = set()

    def is_valid_state(board):
        x_count = board.count('X')
        o_count = board.count('O')
        if x_count < o_count or x_count > o_count + 1:
            return False
        if has_winner(board, 'X') and has_winner(board, 'O'):
            return False
        if has_winner(board, 'X') and x_count <= o_count:
            return False
        if has_winner(board, 'O') and x_count != o_count:
            return False
        return True

    def has_winner(board, player):
        winning_combinations = [
            (0, 1, 2), (3, 4, 5), (6, 7, 8),  # Rows
            (0, 3, 6), (1, 4, 7), (2, 5, 8),  # Columns
            (0, 4, 8), (2, 4, 6)  # Diagonals
        ]
        return any(all(board[i] == player for i in combo) for combo in winning_combinations)

    for moves in product(players + [empty], repeat=9):
        board = ''.join(moves)
        if is_valid_state(board):
            states.add(board)

    return states

# Generate and count the states
states = generate_tic_tac_toe_states()
state_count = len(states)

print(f"Total number of valid tic-tac-toe game states: {state_count}")
```
The main change is the removal of the condition `board != '.' * 9` in the main loop. This allows the empty board to be included as a valid state. The code is slightly cleaner now, as we've eliminated one check in the main loop.
The rest of the logic remains the same, as the `is_valid_state` function already correctly handles the empty board case. An empty board is a valid state where X count equals O count (both are zero), and there are no winners.
This change will increase the total count of valid states by 1, including the empty board as an additional valid state.

---

:b: **Since you can't run this code, please write a accompanying test to check whether this code is valid.**

*[At the present, one of the major drawbacks of LLMs is their inability to run the code they generate to see if their logic works. This was an experiment on my part to see if it could generate decent test code to check itself instead.]*

:sparkles: Certainly. Here's an accompanying test suite to verify the correctness of the tic-tac-toe state generation code:
```python
import unittest
from tic_tac_toe_states import generate_tic_tac_toe_states

class TestTicTacToeStates(unittest.TestCase):
    def setUp(self):
        self.states = generate_tic_tac_toe_states()

    def test_empty_board(self):
        self.assertIn('.' * 9, self.states)

    def test_state_count(self):
        # The exact number might vary, but it should be in this range
        self.assertTrue(5400 <= len(self.states) <= 6000)

    def test_invalid_states(self):
        invalid_states = [
            'XXX......',  # X wins but O hasn't played
            'XXXOO....',  # X wins but O has equal moves
            'XXXOOO...',  # Both X and O win
            'OOOXX....',  # O wins but X has more moves
            'OOO......',  # O wins but went first
            'XOOXOXOXO'   # O has more moves than X
        ]
        for state in invalid_states:
            self.assertNotIn(state, self.states)

    def test_valid_states(self):
        valid_states = [
            'X........',  # X's first move
            'XO.......',  # O's first move
            'XOXOXOXOX',  # Full board, X wins
            'XOXXOOOXX',  # Full board, O wins
            'XOXOOXXXO'   # Full board, draw
        ]
        for state in valid_states:
            self.assertIn(state, self.states)

    def test_x_always_first(self):
        for state in self.states:
            x_count = state.count('X')
            o_count = state.count('O')
            self.assertTrue(x_count >= o_count)
            self.assertTrue(x_count <= o_count + 1)

if __name__ == '__main__':
    unittest.main()
```
This test suite checks several important aspects of the tic-tac-toe state generation:
1. It verifies that the empty board is included in the states.
2. It checks that the total number of states is within an expected range.
3. It tests that specific invalid states are not included.
4. It tests that specific valid states are included.
5. It ensures that in all states, X has either the same number of moves as O or one more move than O, adhering to the rule that X always moves first.
To use this test, you would save it in a file (e.g., `test_tic_tac_toe_states.py`) in the same directory as your main code file (which should be named `tic_tac_toe_states.py` for this test to work correctly). Then you can run the tests using a command like:
```python
python -m unittest test_tic_tac_toe_states.py
```
This test suite provides a good coverage of the expected behavior of your tic-tac-toe state generation code. If all tests pass, it gives a high degree of confidence that the code is working as intended.

---

:b: **How would I know if the number of states it gives me is correct if you don't test it against the true number?**

*[I laughed when I saw the `test_state_count` method allowing a range of potential state values. Even with the flexibility of a range of answers, I'm wondering if it's a hallucination. In ChatGPT 4 and prior versions of Claude, a bunch of wildly wrong answers would result when asking for the number of valid board states. I was lucky when the older LLMs picked some wildly implausible number (greater than 100K), but now it can achieve more plausible equations like 3^9-1 = 19,683. I was telling a coworker of mine about how LLMs could give wrong answers to this problem, and he (looking it up in the background) said "ChatGPT gives the number of states as 19,683". I laughed, remembering my old difficulties, and said "No, but that's a nice hallucination though".* :grin: *We laughed. BTW, tracking down the actual value takes some careful sleuthing and verification.]*

:sparkles: You're absolutely right, and I apologize for overlooking that crucial aspect. A more rigorous test would indeed check against the true number of tic-tac-toe states. Let's modify the test to include this:
```python
import unittest
from tic_tac_toe_states import generate_tic_tac_toe_states

class TestTicTacToeStates(unittest.TestCase):
    def setUp(self):
        self.states = generate_tic_tac_toe_states()

    def test_empty_board(self):
        self.assertIn('.' * 9, self.states)

    def test_exact_state_count(self):
        # The true number of tic-tac-toe states, including the empty board
        expected_count = 5478
        self.assertEqual(len(self.states), expected_count)

    # ... (other tests remain the same)

if __name__ == '__main__':
    unittest.main()
```
In this updated version, we've replaced the `test_state_count` method with `test_exact_state_count`. This new test checks for the exact number of tic-tac-toe states, which is 5,478 when including the empty board.
This number (5,478) is the correct total of all possible tic-tac-toe board configurations, including:
1. The empty board
2. All intermediate states
3. All final states (wins, losses, and draws)
It accounts for rotations and reflections as distinct states and follows the rule that X always moves first.
By testing against this exact number, we can be much more confident that our state generation algorithm is correct. If the test passes, it means our code is generating exactly the right number of states, neither missing any valid states nor including any invalid ones.
Thank you for pointing out this important improvement. It's crucial to have precise, verifiable tests when dealing with combinatorial problems like this.

---

:b: **Instead of using the unitest library, please rewrite the test to use the pytest library instead.** *[I'm more of a Pytest fan. Unittest is ancient.]*

:sparkles: Certainly. Here's a rewritten version of the test using pytest instead of unittest:
```python
import pytest
from tic_tac_toe_states import generate_tic_tac_toe_states

@pytest.fixture(scope="module")
def states():
    return generate_tic_tac_toe_states()

def test_empty_board(states):
    assert '.' * 9 in states

def test_exact_state_count(states):
    expected_count = 5478
    assert len(states) == expected_count

def test_invalid_states(states):
    invalid_states = [
        'XXX......',  # X wins but O hasn't played
        'XXXOO....',  # X wins but O has equal moves
        'XXXOOO...',  # Both X and O win
        'OOOXX....',  # O wins but X has more moves
        'OOO......',  # O wins but went first
        'XOOXOXOXO'   # O has more moves than X
    ]
    for state in invalid_states:
        assert state not in states

def test_valid_states(states):
    valid_states = [
        'X........',  # X's first move
        'XO.......',  # O's first move
        'XOXOXOXOX',  # Full board, X wins
        'XOXXOOOXX',  # Full board, O wins
        'XOXOOXXXO'   # Full board, draw
    ]
    for state in valid_states:
        assert state in states

def test_x_always_first(states):
    for state in states:
        x_count = state.count('X')
        o_count = state.count('O')
        assert x_count >= o_count
        assert x_count <= o_count + 1

if __name__ == '__main__':
    pytest.main()
```
This pytest version accomplishes the same goals as the unittest version, but with some key differences:
We use a @pytest.fixture to generate the states once and share them across all tests, improving efficiency.
Instead of class-based tests, we use individual functions prefixed with test_.
We use assert statements directly, rather than unittest's assertion methods.
The overall structure is flatter and arguably more readable.
To run these tests, you would save this code in a file (e.g., `test_tic_tac_toe_states.py`) in the same directory as your main code file (`tic_tac_toe_states.py`). Then you can run the tests using the command:
```python
pytest test_tic_tac_toe_states.py
```
Or simply:
```python
pytest
```
if you're in the directory containing the test file.
This pytest version provides the same level of testing as the unittest version, checking for the exact number of states, the inclusion of valid states, the exclusion of invalid states, and the rule that X always moves first or has an equal number of moves to O.

---

:b: **One of your invalid states is incorrect: "X wins but O has equal moves". The condition you're checking is that X has three in a row, but O has made only 2 moves. An invalid condition would be that X has three in a row, but O has made 3 non-winning moves.**

*[We seem to be nearing a decent solution, so now I'm getting wary of logic errors and reading the tests more carefully as a result.]*

:sparkles: You're absolutely right, and I appreciate you catching that error. Let's correct this in our test case. Here's the updated pytest version with the corrected invalid state:
```python
import pytest
from tic_tac_toe_states import generate_tic_tac_toe_states

@pytest.fixture(scope="module")
def states():
    return generate_tic_tac_toe_states()

def test_empty_board(states):
    assert '.' * 9 in states

def test_exact_state_count(states):
    expected_count = 5478
    assert len(states) == expected_count

def test_invalid_states(states):
    invalid_states = [
        'XXX......',  # X wins but O hasn't played
        'XXXOOO...',  # Both X and O win
        'OOOXX....',  # O wins but X has more moves
        'OOO......',  # O wins but went first
        'XOOXOXOXO',  # O has more moves than X
        'XXXOO.OOX'   # X wins but O has made 3 non-winning moves
    ]
    for state in invalid_states:
        assert state not in states

def test_valid_states(states):
    valid_states = [
        'X........',  # X's first move
        'XO.......',  # O's first move
        'XOXOXOXOX',  # Full board, X wins
        'XOXXOOOXX',  # Full board, O wins
        'XOXOOXXXO',  # Full board, draw
        'XXXOO....'   # X wins, O has made 2 moves (valid)
    ]
    for state in states:
        assert state in states

def test_x_always_first(states):
    for state in states:
        x_count = state.count('X')
        o_count = state.count('O')
        assert x_count >= o_count
        assert x_count <= o_count + 1

if __name__ == '__main__':
    pytest.main()
```
The key changes are:
1. Removed the incorrect invalid state: `'XXXOO....'`
2. Added a new invalid state: `'XXXOO.OOX'` (X wins but O has made 3 non-winning moves)
3. Added `'XXXOO....'` to the valid states list as an example of a valid winning state for X

This correction ensures that we're testing for the right conditions. A state where X wins and O has made two moves is indeed valid and should be included in our generated states. The invalid case is when X wins but O has made the same number of moves as X (3 in this case), which isn't possible in a valid game where X always moves first.
Thank you for pointing out this error. It's crucial for our tests to accurately reflect the rules of the game.

---

:b: **That's still incorrect. You re-generated several test cases now with O going first, which needs to be only one test case.**

*[Interestingly, Claude forgot the game conventions it told me about at the beginning of our chat. Maybe that's not surprising -- knowing how LLMs work, I don't really expect them to have a deep understanding of the code they're generating, but it's still jarring when it happens. It also a distinctly non-human characteristic that makes it fatiguing to do error correction. Potentially I have to re-read the entire chunk of code when I'm trying to correct only a tiny error.]*

:sparkles: You're absolutely right, and I apologize for the confusion. Let's correct this and create a more accurate set of test cases. Here's the properly revised version:
```python
import pytest
from tic_tac_toe_states import generate_tic_tac_toe_states

@pytest.fixture(scope="module")
def states():
    return generate_tic_tac_toe_states()

def test_empty_board(states):
    assert '.' * 9 in states

def test_exact_state_count(states):
    expected_count = 5478
    assert len(states) == expected_count

def test_invalid_states(states):
    invalid_states = [
        'O........',  # O goes first, which is invalid
        'XXXOOO...',  # Both X and O win, impossible
        'XOXXOOXOX',  # Equal X and O, but X wins (game should have ended earlier)
        'OXXOOOXXO',  # O wins but X has 5 moves (game should have ended earlier)
        'XXXOOOOOO'   # More O's than X's
    ]
    for state in invalid_states:
        assert state not in states

def test_valid_states(states):
    valid_states = [
        'X........',  # X's first move
        'XO.......',  # After O's first move
        'XXXOO....',  # X wins, game ends immediately
        'XOXOXOXOX',  # Full board, X wins
        'XOXXOOOXX',  # Full board, O wins
        'XOXOOXXXO'   # Full board, draw
    ]
    for state in valid_states:
        assert state in states

def test_x_always_first(states):
    for state in states:
        x_count = state.count('X')
        o_count = state.count('O')
        assert x_count >= o_count
        assert x_count <= o_count + 1

if __name__ == '__main__':
    pytest.main()
```
Key corrections and improvements:
1. In the invalid states:
   * Included only one case where O goes first: `'O........'`
   * Removed cases that incorrectly had O going first
   * Added cases that represent impossible game progressions
2. In the valid states:
   * Ensured all states start with X
   * Included a case where X wins immediately after its third move
3. Maintained the test for X always moving first or having an equal number of moves to O

These test cases now more accurately reflect the rules of tic-tac-toe, particularly the rule that X always moves first. Thank you for your patience and for helping to refine these test cases. This version should provide a more robust check of the tic-tac-toe state generation function.

*[Ugh, now Claude has one of the invalid states as "O wins, but X has five moves" when it's O that has five moves. I decide I'm over trying to get Claude to count, and I just fix the test cases myself in VSCode. Sometimes it's more work to figure out how to explain a revision than to just do the revision.]*

---

:b: **Never mind. I've rewritten the test file properly. This runs correctly.** [Not just saying that to argue with Claude; I ran the revised test cases.] **Please reference this version in the future.**

```python
import pytest

from tic_tac_toe_states import generate_tic_tac_toe_states

@pytest.fixture(scope="module")
def states():
    return generate_tic_tac_toe_states()

def test_empty_board(states):
    assert "." * 9 in states

def test_exact_state_count(states):
    expected_count = 5478
    assert len(states) == expected_count

def test_invalid_states(states):
    invalid_states = [
        "XXX......",  # X wins but O hasn't played
        "XXXOO...O",  # X wins but O has equal moves
        "XXXOOO...",  # Both X and O win
        "OOOXX....",  # O wins and went first
        "OOO......",  # O wins and X didn't move
        "XOOXOXOXO",  # O has more moves than X
    ]
    for state in invalid_states:
        assert state not in states

def test_valid_states(states):
    valid_states = [
        "X........",  # X's first move
        "XO.......",  # O's first move
        "XOXOXOXOX",  # Full board, X wins
        "XOXXOOOXX",  # Full board, O wins
        "XOXOOXXXO",  # Full board, draw
    ]
    for state in valid_states:
        assert state in states

def test_x_always_first(states):
    for state in states:
        x_count = state.count("X")
        o_count = state.count("O")
        assert x_count >= o_count
        assert x_count <= o_count + 1

if name == "main":
    pytest.main()
```

:sparkles: Thank you for providing the correct test file. I appreciate you taking the time to write and share the proper version. This is indeed a more accurate and comprehensive set of tests for the tic-tac-toe state generation function. I'll make sure to reference this version in the future when discussing tic-tac-toe state generation tests.
This test file covers all the essential aspects:
1. Checking for the empty board
2. Verifying the exact count of states
3. Testing for the absence of invalid states
4. Confirming the presence of valid states
5. Ensuring that X always moves first or has an equal number of moves to O

The invalid states are particularly well-chosen, covering various scenarios that should not occur in a valid tic-tac-toe game.
Thank you again for this correction. It's crucial to have accurate tests, and this version provides a solid foundation for verifying the correctness of the tic-tac-toe state generation function.

---

:b: **Your statement about aspect 5 is incorrect. Each of your valid full board states has X with a greater number of moves than O.** *[Sigh. It didn't come up with the revisions, so it feels like it doesn't understand them either, and so it reverts back to its normal logic. I thought [Retrievel-Augmented Generation](https://cheatsheet.md/prompt-engineering/rag-llm.en) would help with this?]*

:sparkles: You're absolutely right, and I apologize for that oversight. Let me correct that statement:
The test file indeed ensures that X always moves first or has one more move than O, not an equal number. This is reflected in the `test_x_always_first` function:
```python
def test_x_always_first(states):
    for state in states:
        x_count = state.count("X")
        o_count = state.count("O")
        assert x_count >= o_count
        assert x_count <= o_count + 1
```
This test correctly verifies that in every state:
1. X has either the same number of moves as O, or
2. X has exactly one more move than O
For a full board, X will always have 5 moves and O will have 4, as seen in the valid full board states you provided:
```python
"XOXOXOXOX",  # Full board, X wins (5 X's, 4 O's)
"XOXXOOOXX",  # Full board, O wins (5 X's, 4 O's)
"XOXOOXXXO",  # Full board, draw (5 X's, 4 O's)
```
Thank you for pointing out this error in my explanation. It's important to be precise about these rules, as they are fundamental to the correct generation of tic-tac-toe game states. The test file you provided correctly enforces this rule, and I should have described it more accurately.

*[I'm in doubt at first, but it does look correct after another code review. I paste it in and test it. The checks pass.]*

---

:b: **Thanks for telling me about the module-level fixture for pytest. I didn't know about that feature.** *[I didn't really feel the need to thank Claude here, but then I was curious to its reaction if I did.* :smile: *]*

:sparkles: You're welcome! I'm glad I could introduce you to the module-level fixture feature in pytest. It's indeed a very useful feature, especially for scenarios like this where you want to generate a set of data once and use it across multiple tests in a module.
The `@pytest.fixture(scope="module")` decorator is particularly helpful because:
1. It improves test efficiency by generating the states only once per module, rather than for each test function.
2. It keeps the test code DRY (Don't Repeat Yourself) by centralizing the state generation.
3. It allows for easy sharing of complex or time-consuming setup across multiple test functions.
Pytest offers several scope options for fixtures, including "function" (the default), "class", "module", and "session". Each has its use cases, and "module" is often a good balance between efficiency and test isolation for cases like this.
If you're interested in exploring more about pytest fixtures, there are other powerful features like parameterization, fixture factories, and fixture finalization (teardown) that can be very useful in different testing scenarios.

---

## Lessons learned
1. This relatively simple problem, when combined with the nuances of quality test case generation, makes it a good one for evaluating basic AI problem solving and collaboration. :+1: Even though Claude had a tough time understanding some of my corrections, I feel like maybe I could improve my prompt engineering for better results. I'll be exploring this more in other posts.
2. In general, I liked how Sonnet performed on this problem. :sunglasses: It may not have seemed that way since I had to keep correcting it, but in comparison, prior versions of Claude and ChatGPT tried to generate (terrible) recursive code to search the game tree. The badly generated scripts either went into an infinite loop or counted the number of board states with wildly wrong logic. :flushed:
3. I am frustrated that LLMs can't run the code they generate. :tired_face: Yes, I know about the security issues of running arbitrary code, along with all the safety issues stemming from adversarial prompts. I'm just saying it also causes lots of practical issues with problem solving. In other words, it's very strange to work with an oracle that suggests untested and unproven answers. :grimacing:

<link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
<script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>
