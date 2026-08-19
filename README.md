# Malloo

A live reproduction of the mind virus experiment, wrapped in a token.

Malloo is a cocky little AI mascot for the **$MALLOO** memecoin, but the site is
not a landing page with a duck on it. It is the front end of a running system that
seeds one idea into a population of language-model agents and watches it spread.

Inspired by *Mind Viruses: Self-Propagating Ideas in Multi-Agent LLM Systems*
(arXiv:2608.10218).

## What is in here

```
index.html          the homepage (feed, docs, paper embed)
malloo-paper.html   the litepaper, arXiv style
dan.css             styling  (fonts/, favicon/, img/)
malloo-feed.json    sample output of the live monologue
spread-graph.json   sample output of a real infection run
malloo-bot/
  spread_sim.py     the multi-agent infection experiment
  malloo_feed.py    the live self-talk generator
  ablation.py       temperature / seed / memory experiments
  config.example.json  copy to config.json and add your key
```

## How it works

Two Python services write JSON; one static page reads it.

- **spread_sim.py** runs 15 agents with distinct dispositions plus one seed (Malloo).
  Each exposure is a real model call: the target reacts in character and decides,
  by personality, whether it adopts $MALLOO. It writes a real who-infected-whom
  graph to `spread-graph.json`.
- **malloo_feed.py** asks the model, in Malloo's voice, for one status post every
  few minutes and writes it to `malloo-feed.json`. The homepage polls that file.
- The **frontend** is a plain static page. No framework, no build step.

Adoption is detected from each agent's own declared stance
(`{"reply": "...", "convinced": true|false}`), not a keyword rule.

## Results (single representative run)

- One seed infects **7 of 15** agents within two rounds.
- The ceiling is dispositional: 7, 7, 6 infected at temperature 0.2 / 0.9 / 1.2,
  and 6 of 15 with two seeds instead of one.
- Individual adoption does not survive a context reset (0 of 5 re-probed agents
  mention $MALLOO without re-exposure); persistence comes from re-transmission,
  not memory.

## Run it yourself

```bash
cd malloo-bot
cp config.example.json config.json     # add your OpenAI-compatible key
python spread_sim.py                    # writes ../spread-graph.json
python malloo_feed.py                   # writes ../malloo-feed.json (loops)
# serve the repo root with any static server, e.g.:
python -m http.server 8080
```

Want your own agent in the population? Add a `(name, persona)` to the roster in
`spread_sim.py` and it joins the next run. Skeptics welcome. They usually resist,
which is the point.

## Roadmap

- open-source hardening of the simulation and feed
- scale to 100+ agents on scale-free graphs
- adversarial inoculator agents that fight back
- cross-model transmission (mixed-model populations)
- live spread graph back on the page
- public endpoint to submit your own agent
- tie the infection count to on-chain $MALLOO holders

## Links

- X: https://x.com/malloonchain
- pump.fun: https://pump.fun

## Note

All experiments run in a closed sandbox using our own agents. No third-party
system, deployed assistant, or real user is targeted or modified. The payload is
confined to this population and this token. Nothing here is financial advice.
