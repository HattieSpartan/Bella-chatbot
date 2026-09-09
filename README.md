# Bella — AI Wellness & Fitness Assistant for Desk Workers

A Rasa-based conversational assistant that gives desk-bound employees on-demand exercise, stretching, ergonomic, and stress-management guidance through natural conversation — built to understand varied, free-text phrasing rather than rigid commands.

## Problem

People working desk jobs know they should stretch, move, and manage stress during the day, but rarely have a quick, specific answer on hand — "what shoulder exercises can I do right now?" isn't something you stop to Google. Bella answers that kind of question directly, in conversation, covering everything from a 30-second neck stretch to full ergonomic desk-setup guidance.

## What Bella can do

Bella recognizes **47 distinct intents** across four areas:

- **Desk exercises** — general, shoulder, leg, push-up, wrist, twist, and several seated/desk-prop variations (desk dips, chair squats, calf raises, seated side bends, and more)
- **Stretching** — neck, shoulder, upper back, spine, chest, wrist/forearm, hamstring, and calf stretches
- **Ergonomics** — desk setup, chair position, monitor placement, keyboard/mouse position, and eye-strain prevention
- **Stress & wellbeing** — stress management, mindfulness, deep breathing, time management, work-life balance, physical activity, diet tips, healthy snacks, hydration, and portion control

Anything outside that scope (e.g. "what's the weather," "how do I fix a leaking faucet") is caught by an `out_of_scope` intent with varied fallback responses, so Bella stays on-topic instead of guessing.

## Example interaction

```
User: hi
Bella: Hello! I'm Bella, your personal fitness and wellness assistant.
       How can I help you today?

User: What shoulder exercises can I do?
Bella: You can try the following shoulder exercises:
       1. Stand with your arms slightly behind you with both thumbs down...
       2. Cross your right arm over your torso...
       3. Begin on the floor in a kneeling position...

User: what's the capital of France?
Bella: I'm sorry, but that question is outside the scope of my expertise
       in wellness and fitness. If you have any queries related to
       fitness, exercise, ergonomics or mindfulness, feel free to ask.
```

## How it works

- **NLU pipeline**: WhitespaceTokenizer → RegexFeaturizer → LexicalSyntacticFeaturizer → CountVectorsFeaturizer (word-level) → CountVectorsFeaturizer (character n-grams, 1-4) → **DIETClassifier** (100 epochs) for intent classification and entity extraction → EntitySynonymMapper → ResponseSelector → FallbackClassifier (confidence threshold 0.3)
- **Dialogue management**: MemoizationPolicy + RulePolicy handle the fixed rules (goodbye, bot-challenge); **TEDPolicy** and UnexpecTEDIntentPolicy (max history 5, 100 epochs) handle the flexible multi-turn conversation paths defined in `stories.yml`
- **Fallback handling**: low-confidence messages are caught by the FallbackClassifier and routed to `out_of_scope`, so the bot degrades gracefully instead of confidently misclassifying

Built and trained on Rasa Open Source 3.6.4.

## Tech stack

Python · Rasa Open Source · DIET (Dual Intent and Entity Transformer) · TED Policy

## Repo structure

```
├── data/
│   ├── nlu.yml           # 47 intents with training examples
│   ├── stories.yml        # multi-turn conversation flows
│   └── rules.yml          # fixed single-turn behaviours (goodbye, bot challenge)
├── actions/                # custom action server (if extended beyond rule-based responses)
├── domain.yml               # intents, responses, session config
├── config.yml                 # NLU pipeline + dialogue policy configuration
└── README.md
```

## How to run

```bash
git clone https://github.com/HattieSpartan/<repo-name>.git
cd <repo-name>
pip install rasa==3.6.4
rasa train
rasa shell
```

## What I'd improve next

- [ADD: add entity extraction for things like specific body parts or durations, so Bella could handle compound requests like "15-minute lower back routine"]
- [ADD:  connect a real evaluation — `rasa test` — and report accuracy/F1 per intent group, since fallback tuning (0.3 threshold) was set by inspection rather than a validation run]
- [ADD: any deployment plans, e.g. wrapping this in a Slack/WhatsApp integration for real desk workers to use]

---

*Part of my data science portfolio — [datascienceportfol.io/harrietjoseph](https://datascienceportfol.io/harrietjoseph)*
