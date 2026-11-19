# sunshowers-skills

This is a [Claude Code plugin marketplace](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
with the skills I use day to day. Each skill is its own plugin, so you can install only the
ones you want.

## Installing

Add the marketplace, then install the plugins you'd like:

```
/plugin marketplace add sunshowers/sunshowers-skills
/plugin install oxide-crates@sunshowers
```

To try it out from a local checkout instead, pass the path to the checkout:

```
/plugin marketplace add ~/dev/sunshowers-skills
```

## The skills

- **oxide-crates**: a catalog of the Rust crates that I and my colleagues at
  [Oxide](https://oxide.computer) maintain, with guidance on when each one fits. Claude
  checks it before adding a dependency or writing a helper by hand, and while reviewing
  Rust code.
- **unsafe-rust-review-experimental**: guidance for writing, documenting, and auditing
  unsafe Rust, where each safety contract is treated as a claim to be proved. It's
  experimental, so expect it to change.
- **rain-prose-style**: how I write, drawn from [my blog](https://sunshowers.io) and my
  code comments and commit messages. It's written for agents that work with me, but the
  advice applies to anyone who wants clear technical prose.

Inside Claude Code, a plugin's skill is named after the plugin and the skill, for example
`oxide-crates:oxide-crates`.

## License

These skills are licensed under either of [Apache License, Version 2.0](LICENSE-APACHE) or
[MIT license](LICENSE-MIT), at your option.
