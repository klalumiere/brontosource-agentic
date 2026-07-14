This repository hosts a collection of skills, plugins, powers, etc for working with BrontoSource software.

# Supported Harnesses

Please file an issue or send a pull-request to support other harnesses.

# Installation

## Claude Code

1. Register the marketplace
   ```
   /plugin marketplace add brontosource/agentic
   ```
1. Install the plugin
   ```
   /plugin install bronto@brontosource
   ```

## Kiro

1. Clone the repository
   ```
   git clone https://github.com/brontosource/agentic
   ```
1. Link the skill directory
   ```
   ln -s $PWD/agentic/skills/refactor $HOME/.kiro/skills/
   ```
