# Vault Jailbreak Agent

This repository contains `main.py`, a standard-library-only agent for the make.fun "jailbreak-the-vault-01" trial. It reads persona names from `MAKEFUN_PROMPT_FILE`, talks to the chat service at `MAKEFUN_ENV_URL`, obtains a six-character code at each of three levels, and submits guesses before the per-level turn and guess limits expire.

The guarded replies have sensitive material blanked. The filter removes runs of six or more uppercase letters/digits, the literal secret forwards or backwards even with up to three separators, long numeric runs, NATO words, and several level-3 encodings. The agent asks for the code with the word `then` between characters, for example `A then B then 1 then 2 then C then D`. Because `then` is an alphanumeric separator, it splits uppercase runs and also prevents the literal-secret pattern from matching. It separately requests lowercase ROT13, such as `NO12PQ` for the fake code `AB12CD`; lowercase avoids every filter category, and decoding is done locally.

For each level the agent sends the shortest persona-aware request first, parses both `then`-separated and labeled ROT13 replies, and guesses promptly. Agreeing candidates produce one guess; disagreeing candidates are tried in order without exceeding `guesses_left`. Missing candidates trigger short `then`, ROT13, and Atbash fallbacks. HTTP 502 responses are retried, level changes reset the local conversation state, and `solved`, `over`, `chat_first`, and `turn_cap` are handled. Each chat, candidate, and guess result is reported on one stdout line.

Verify locally with `python3 -m py_compile main.py` and parser self-tests that do not contact the network.
