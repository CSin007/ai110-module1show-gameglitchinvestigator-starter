# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the hints were backwards").
- it looks like a game to input a random number and it has to be guessed, the strange thing is I can see the random number I am supposed to guess. The bug is new game does not start a new game, also go higher and go lower as hints perform opposite funciton than intended
---

## 2. How did you use AI as a teammate?

- I used Claude (Claude Code) as my AI tool for this project. I described the bugs I found and asked Claude to explain the logic, refactor the code, and apply the fixes directly.

- **Correct suggestion:** Claude correctly identified that in `check_guess`, the condition `if guess > secret` was returning `"Go HIGHER!"` when it should return `"Go LOWER!"` — the hints were backwards. Claude explained step-by-step that when your guess is *too high*, the correct advice is to guess lower. I verified this by running `pytest`, where `test_guess_too_high` confirms that `check_guess(60, 50)` returns `"Too High"`, and by playing the game live to confirm the hint matched the expected direction.

- **Incorrect/misleading suggestion:** Claude's initial refactor moved all functions into `logic_utils.py` correctly, but pytest immediately failed with `ModuleNotFoundError: No module named 'logic_utils'`. The AI had not accounted for the fact that pytest runs from the `tests/` subdirectory and cannot see the project root by default. Claude then corrected this by adding a `conftest.py` at the project root. I verified the fix by running `pytest` again and seeing all 6 tests pass.

---

## 3. Debugging and testing your fixes

- I decided a bug was fixed when (1) the relevant pytest test passed, and (2) I manually confirmed the behavior in the live Streamlit app using `streamlit run app.py`.

- I ran `pytest` from the project root after all fixes were applied. The output showed 6 tests passing: the 3 original starter tests (`test_winning_guess`, `test_guess_too_high`, `test_guess_too_low`) confirmed that `check_guess` now returns the correct outcome strings. The 3 new tests I added (`test_parse_guess_valid`, `test_parse_guess_empty`, `test_parse_guess_non_number`) confirmed that `parse_guess` handles valid input, empty input, and non-numeric input correctly.

- Yes — Claude helped me understand that the existing starter tests were actually written to test the *fixed* behavior, not the buggy behavior. This meant the tests were already the right verification tool; I just needed to fix the code to make them pass. Claude also suggested adding `parse_guess` tests since that function was being moved to `logic_utils.py` and had no coverage yet.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.
