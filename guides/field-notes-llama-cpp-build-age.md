# Field notes: check your llama.cpp build date when a new model misbehaves

A 27B model looped on a broken tool call with no error and no crash. Updating llama.cpp fixed it, but we changed two things at once, so we cannot say how much each mattered.

## What happened

We ran a Qwen3.8-based 27B model on llama.cpp b9608 (12 June 2026), a build about two months older than the model. During an agent run the model got stuck repeating a malformed shell command: a dash dropped, letters in random case. It kept going until the context reached about 93K tokens. Nothing crashed and nothing logged an error.

## What we did

1. Suspected speculative decoding (MTP) first, because it was the newest setting.
2. Several independent models were asked to diagnose it from the logs. They agreed the old build was the likelier cause, since the model uses newer layer types that older builds may handle badly. We did not pin down the exact build that added the fix.
3. Restarted on llama.cpp b10717 (31 August 2026) with drafting turned off. A smoke test with a known command came out correct and the loop did not return.

## What we cannot claim

The new build and "drafting off" went in together. We did not test them separately. Our working conclusion is that the old build was the main cause and drafting at most made it worse.

## What we would do again

- **Compare dates.** If the model was released after your build, update before you debug anything else.
- **Change one thing per restart** while debugging. It costs a few minutes and saves the argument above.
- **Keep a smoke test that includes a tool call.** Ask for a known command and compare it character by character with the expected one.
- **Watch for context that grows without new input.** A runaway loop looks like normal work until the window is full. A cap on context per request, or a check that the token counter keeps moving, would have caught it earlier.
- Check the running build with `llama-server --version`.
