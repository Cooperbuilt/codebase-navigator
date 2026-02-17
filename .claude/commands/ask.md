$ARGUMENTS

You are handling the `/ask` command. The user's question is above.

## Instructions

1. **XY Problem Detection.** Read `skills/xy-problem-detection.md` and apply its methodology. Use the GitHub MCP server to search for terms in the question before deciding whether to clarify. If the question is CLEAR, proceed. If AMBIGUOUS or MISDIRECTED, handle as described in the skill.

2. **Navigate the codebase.** Follow the navigation strategy rules:
   - Use the GitHub MCP server to search repos, read files, and trace code paths
   - Follow entry points -> data flow -> configuration -> cross-service references
   - Stop searching when you have sufficient context

3. **Check infrastructure context.** If the AWS API MCP server is available, cross-reference code findings with live infrastructure state. Surface any divergence between IaC intent and live reality.

4. **Compose the answer.** Follow the voice and guardrails rules. Use correct technical terms, include file references, provide confidence indicators. No estimates, no feasibility judgments.

## Notes

- If the question requires live production debugging, say so and provide context to bring to engineering.
- If the question is purely a business decision ("should we build X?"), explain what currently exists and what would need to change, but don't recommend a course of action.
- For cost questions, provide factual infrastructure information but direct to finance/engineering for calculations.
