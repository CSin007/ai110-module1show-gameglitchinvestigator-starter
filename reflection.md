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

Every time a user clicks a button or types something in a Streamlit app, the entire Python script reruns from top to bottom — like refreshing a page. Without `st.session_state`, any variable you set (like the secret number or the attempt count) would reset to its starting value on every rerun. `session_state` is like a small notebook that Streamlit keeps between reruns, so values persist across interactions. This is why the New Game bug was subtle — resetting `attempts` and `secret` wasn't enough; `status` also needed to be reset, otherwise the script would rerun, hit the `status != "playing"` check, and immediately stop the game again.

---

## 5. Looking ahead: your developer habits

- **Habit to reuse:** Running `pytest` after every fix, not just at the end. Having the tests already written (even if they were failing) meant I always had a clear pass/fail signal for whether my change actually worked. I'll write tests first in future projects.

- **Do differently next time:** I would verify AI-suggested code in a smaller piece before applying it to the whole file. When Claude refactored all four functions at once, the import error from `conftest.py` being missing took extra back-and-forth to resolve. Testing one function move at a time would have caught that faster.

- **How this changed my thinking:** AI-generated code looks confident and complete but can have subtle logic inversions (like the hint directions) or structural gaps (like missing path configuration for imports) that only show up when you actually run it. I now treat AI output as a strong first draft that still needs to be read line-by-line and verified with tests.
