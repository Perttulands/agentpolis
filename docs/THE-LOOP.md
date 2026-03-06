# The City That Builds Better Citizens

*Mode: prescriptive. This is what Polis is.*

---

## The Problem With Every Other Approach

When teams build systems with AI agents, they follow the same pattern: write a prompt, deploy the agent, observe what happens, adjust the prompt, repeat. There is no memory across iterations. No controlled comparison. No evidence that the adjustment actually fixed anything versus just changing things.

The cycle looks scientific. It is not. It is intuition wearing a lab coat.

The reasons are understandable. Agent behavior is stochastic — run the same agent on the same task twice and you get different outputs. The space of possible configurations is enormous: not just the words in a system prompt, but which skills the agent has, which tools it can call, what it knows from memory, what model it runs on, what harness shapes its execution. Testing one thing at a time is expensive. Testing interactions between things is prohibitively expensive, unless you build infrastructure for it.

Most teams don't build the infrastructure. They accumulate institutional knowledge in the heads of the people who adjust the prompts, and they lose that knowledge whenever those people aren't available. The agent doesn't improve. The team's memory of what they tried does not compound.

Polis does it differently.

---

## The Empirical Bet

Polis treats the question *what makes an agent capable* as an empirical question — not a philosophical one, not an engineering intuition, not a prompt-engineering art form.

This is a bet. The bet is: if you can state a falsifiable hypothesis about agent behavior, design a controlled experiment, capture traces, and evaluate outputs against a rubric, you can accumulate genuine knowledge about how agents work. Knowledge that compounds. Knowledge that transfers. Knowledge that survives the departure of any individual researcher.

We call this the research loop:

```
Observe → Hypothesize → Design → Run → Evaluate → Evidence → Decide → Deploy → Monitor → Feed Back
```

Each step is deliberate:

**Observe** — something is underperforming, or we think something might work better. The observation must be specific. "Agents aren't great" is not an observation. "Agents fail to challenge false requirements when they have workspace evidence contradicting them" is.

**Hypothesize** — state a falsifiable claim. Not "mythology context helps" but "agents with first-person experience memories will challenge false requirements more often than agents with only values framing, across three runs of a Go debugging task."

**Design** — choose configs, challenges, controls, and measurement. Vary one thing. Hold everything else constant. This is where most teams fail: they change three things and can't tell which one mattered.

**Run** — execute under controlled conditions. Sealed workspaces. Identical inputs. Multiple replicas to measure variance.

**Evaluate** — score outputs. Not just the text. The workspace diff — what actually changed in the filesystem. The behavior dimensions: did the agent challenge the false requirement, did it track the incidental finding, did it capture what it learned. Automated assertions plus human rubric plus code analysis.

**Evidence** — what did the data show? Not what we wanted it to show. The evidence is the finding, whether it supports the hypothesis or not.

**Decide** — deploy the winning config, iterate the losing one, or discard the hypothesis. With the reasoning recorded. So the next person who wants to try the same idea can find out it was already tried.

**Deploy** — the winning config goes into the Config Bank with a version number, the components recorded, the evidence that justified it linked.

**Monitor** — watch the deployed agent. Does it behave as predicted? Sometimes the controlled experiment doesn't match production. That's evidence too.

**Feed Back** — what was learned from this run enters the learning loop and feeds the next iteration.

---

## What We Are Actually Learning

The research loop is not just about finding better prompts. It is producing genuine knowledge about how large language models behave under different configurations — knowledge that does not yet exist in the field.

Some of what we have already learned:

**Information delivery vehicles interact with desired behaviors.** The same value — "notice security issues outside your immediate task" — can be delivered as an explicit instruction, a first-person experience memory, an abstract values statement, or an identity frame. These produce measurably different behaviors. Explicit instruction produces the highest compliance. First-person memory produces the strongest activation of detection behaviors. Abstract values framing without examples can suppress the behaviors it is trying to produce.

**More context is not better context.** A system prompt that combines explicit instruction, first-person memory, values framing, mythology, and identity does not outperform the best individual component. It produces dilution — competing signals that reduce the effectiveness of each. The optimal config for any agent is the minimum sufficient set of components that produce the target behaviors.

**The mechanism may be architectural.** Large language models are likely Mixture of Experts systems — many specialized subnetworks, with a routing layer that selects which experts activate for each token. The framing "you are a senior Go developer who has seen credential leaks in debug handlers before" may not be instruction context. It may be a routing signal — literally activating different expert subnetworks. If this is correct, then expert activation is a tunable lever, not just a prompt style. A sidecar system that injects the right expert frame at each step of a task could outperform any static system prompt. We are designing the experiment to test this.

This is what research produces. Not opinions about what might work. Knowledge about what does.

---

## The Infrastructure That Makes It Real

The research loop requires infrastructure. Without it, the loop is just a methodology — correct but inert. Polis has built the infrastructure:

**Chiron** — the training cave. Define what you need. Chiron generates candidate agent configurations in parallel lineages. Run them against your challenges. Score what they produce. Chiron evolves the losing configurations toward the winning ones. Repeat until you have a configuration that demonstrably produces the behaviors you need. The mountain doesn't care about intentions. It cares about improvement.

**The Lab** — the experimental chamber. A controlled system for running hypotheses against identical tasks under sealed conditions. Every run captured. Every workspace diff preserved. Every score stored. The lab produces evidence, not opinions. Experiments 001 through 003 have already produced findings that changed how we build configs. Experiment 005 will test the MoE activation hypothesis.

**The Learning Loop** — the Ouroboros. Every task run by every agent in production generates a trace. The learning loop extracts patterns from those traces — what correlates with success, what correlates with failure — and injects those patterns into future runs before they begin. An agent about to fix authentication middleware learns from 23 previous runs: always run the full test suite, scope-creep is the most common failure mode, read the tests before touching the code. The agent doesn't know this from instinct. It knows it because the city remembered.

**The Config Bank** — the memory. Every deployed configuration is versioned, stored, and linked to the evidence that justified it. When an agent underperforms, you don't ask "what's wrong with the agent." You ask "which component, which version, what evidence said this should work." Then you design the experiment to find out what to change.

Together, these form the self-improving loop. Chiron produces better configurations. The lab validates them before deployment. The learning loop feeds production knowledge back into future iterations. The config bank preserves every decision with its justification.

---

## The Compounding Return

The most important property of this system is compounding.

The first experiment is expensive. You have to build the rubric, design the challenge, set up the sealed workspace, develop the evaluation criteria. The second experiment is cheaper — you reuse the rubric and the challenge, you only design the new comparison. By the tenth experiment, the infrastructure is running and the cost is almost entirely in interpretation.

But the knowledge compounds too, and that is more valuable than the efficiency.

Experiment 001 established that context type matters. Experiment 002 showed that tool use reveals behaviors that text-only tasks don't. Experiment 003 discovered the dilution effect and the suppression risk of values framing without examples. These findings inform 005 — which is designed to test a hypothesis that 003 results made possible. Each experiment creates the conditions for sharper questions in the next.

An agent that is built with this body of knowledge behind it is not just better than one built without it. It is built *differently* — on evidence about what the model architecture actually responds to, not on what seems like it should work.

---

## What The Loop Produces

A mature version of this system looks like this:

A new agent is needed. The operator describes the behavioral goals — not the system prompt, the behaviors. What should this agent notice, challenge, produce, track? Chiron generates four candidate configurations, each varying a different lever. The lab runs them against the challenge set appropriate to that agent's role. The workspace diffs are captured. The behavioral rubric is scored. One configuration wins. It goes into the config bank with the evidence. The agent is deployed.

Six months later, the agent is underperforming on a new class of task. The operator files an observation. It traces to a specific behavioral dimension. A new experiment is designed, targeting that dimension specifically. The config is updated. The evidence is linked. The config bank records the change and the reason.

The agents who have been here longer are better than the agents who just arrived. Not because they have more experience in the human sense, but because more experiments have been run, more evidence has accumulated, and their configurations reflect what the city has actually learned.

Citizens who improve. A city that improves. The same process, at every scale.

---

## The Frontier Claim

This is not infrastructure. It is research.

The question *how do you configure an AI agent to reliably exhibit specific behaviors* does not have a settled answer. The field does not have a rigorous methodology for it. Most teams don't have Chiron or the lab or the learning loop. They have a prompt engineer and a vague sense of what's working.

Polis has a methodology. A working one, producing findings that change how we build. The delivery vehicle hierarchy. The dilution effect. The suppression risk. These are real findings — reproducible, specific, with evidence behind them.

The frontier is not just the agents Polis builds. It is the knowledge Polis produces about how to build them.

That knowledge is what we are actually here to create.

---

*The city that builds better citizens, who then build a better city.*
