# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.1] - 2025-05-28

### Changed
- **CRITICAL**: Complete refactoring of MAWEP documentation to properly distinguish between pods (persistent) and agents (ephemeral)
- Updated all state tracking from agent-centric to pod-centric model throughout 17 files
- Revised main repository README.md to accurately describe MAWEP architecture
- Changed all diagrams to show pods as persistent worktrees and agents as Task tool invocations
- Removed misleading references to "spawning" agents - now correctly shows "invoking" agents
- Updated orchestrator prompt to track pods in state file, not agents
- Fixed scenario walkthrough to remove Redis/message bus references and use Task tool model

### Fixed
- Corrected conceptual confusion where agents were treated as persistent entities
- Fixed state file examples to track pods instead of agents (pods have status, not agents)
- Updated all "Agent-N" references to "Pod-N" in examples and diagrams
- Clarified that orchestrator invokes agents FOR pods, not manages agents directly
- Fixed coordination branch pattern to reference pods, not agents

## [0.2.0] - 2025-05-27

### Added
- Reality Check Protocol integration - prevents over-engineering and capability oversell
- Enhanced MAWEP terminology clarity (Agent vs Pod distinction)
- User-friendly README with 80s-inspired fun factor while maintaining professionalism
- Knowledge confidence assessment in Reality Check Protocol
- Complete enterprise-grade prompt pack integration with verified referential integrity

### Changed
- Updated MAWEP framework to use "pod" terminology for persistent git worktrees
- Improved all role prompt templates with pod-based language
- Enhanced orchestrator instructions with proper state management
- Redesigned README.md with better user onboarding and educational content
- Streamlined quick start experience with clearer step-by-step guidance

### Fixed
- Corrected all file path references for prompt pack portability
- Updated broken @ references in global and project CLAUDE.md files
- Aligned framework documentation with current pod-naming conventions

## [0.1.0] - 2025-01-27

### Added
- Initial release of Prompt Packs repository
- MAWEP (Multi-Agent Workflow Execution Process) module
  - Orchestrator prompt for managing parallel AI agents
  - Agent prompt templates for development workers
  - Technical and architectural reviewer prompts
  - Post-mortem analyst prompt
  - Comprehensive documentation and diagrams
  - Example usage scenarios
- Repository structure and documentation
- MIT License
- Installation guidance for both global and project-specific setups
- CONTRIBUTING.md with portability guidelines
- Verification that all MAWEP @ references are relative for maximum portability