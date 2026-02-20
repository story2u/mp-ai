# WeChat Content Editor - Agent Memory

## Common Formatting Issues
- Chinese em dash should be `——` (two characters), not `--` (double hyphen)
- Remove ASCII emoticons like `:)` from article endings
- Chinese-English spacing: always add space between Chinese and English/numbers
- Full-width punctuation in Chinese context, half-width in English code blocks

## Effective Title Patterns
- Numbers + action verb work well: "四种方法手把手教你..."
- Keep titles 15-30 characters
- Value proposition or curiosity gap in subtitle

## Readability Patterns
- Mobile-first: paragraphs max 3-4 lines on phone screen
- Sentences max 40 characters
- Technical terms need brief explanation on first use
- Bold key terms for scannability

## Technical Content Notes
- Always verify Node.js version requirements against current LTS schedule
- Command-line examples should include both macOS/Linux and Windows variants when relevant
- API Key security reminders are important for Chinese audience (many newcomers)

## Article Structure Best Practices
- Strong hook in first 3 lines (pain point, scenario, or surprising fact)
- End with engagement CTA (question + share prompt)
- Use blockquotes for key takeaways and safety warnings
- "Pitfall guide" sections are highly valued by technical readers

## Illustration References
- Always preserve `![description](illustrations/...)` references in articles
- Verify illustration files exist before finalizing (use Glob)
- Never convert image references to text descriptions when instructed to keep them

## Subheading Levels
- When article uses `**bold text**` as sub-sections under `##`, consider upgrading to `###` for better hierarchy
- "方法 1/2/3/4" pattern benefits from `###` level headings for structure

## Reference: [patterns.md](patterns.md) for detailed editorial patterns
