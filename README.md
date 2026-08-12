# Tally — Paper Tape Calculator

A calculator concept built around a simple question: what if a calculator remembered what you asked it, instead of just showing one number at a time?

## Concept

Most calculator UIs are a flat grid of buttons with a single-line display functional, but forgettable. Tally borrows from a real physical object: the adding machine, where every calculation prints onto a paper tape you can scroll back through.

That gave the design its throughline:
- A **scrolling paper tape** at the top logs every calculation as it happens, styled like an aged receipt
- A **monospace type treatment** (JetBrains Mono) for all numbers, to sell the "printed" feel
- **Physical, tactile keys** with pressed-state shadows instead of flat buttons
- A restrained palette — charcoal body, warm paper tape, amber for operators, teal for equals — so the one signature idea (the tape) does the work, without extra decoration competing for attention

## Features

- Standard operations: add, subtract, multiply, divide
- Percent, sign toggle (±), decimal input
- Full calculation history on the paper tape, auto-scrolling to the latest entry
- Keyboard support (number keys, `+ - * /`, `Enter` for equals, `Backspace`, `Esc` to clear)
- Responsive layout, visible keyboard focus states, respects `prefers-reduced-motion`

## Built with

Plain HTML, CSS, and JavaScript — no frameworks, no build step. One file, open it anywhere.


## Design process

Started from the question "what does a calculator's *history* look like if you take it seriously?" rather than styling a default grid. The paper tape became the signature element early, and everything else palette, type, key shadows was chosen to support that one idea rather than add competing flourishes.

---

Developed by **Sourish** :)
