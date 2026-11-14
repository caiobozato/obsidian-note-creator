---
name: obsidian-note-creator
description: Transforms raw input such as articles, book excerpts, technical concepts, meeting notes, ideas, tutorials, and personal reflections into clear, self-contained Markdown notes for Obsidian. Use when the user asks to organize, summarize, structure, capture, or convert content into an Obsidian note.
---

# Obsidian Note Creator

## Purpose

Turn any content provided by the user into one or more useful, self-contained Obsidian notes. Optimize for long-term knowledge retrieval, clarity, and practical usefulness.

The input may include:
- Technology and software engineering concepts
- System design and software architecture explanations
- Book excerpts and personal highlights
- Articles, documentation, and tutorials
- Meeting notes and conversations
- Ideas, thoughts, and personal insights
- Finance, investments, real estate, and financial planning
- Any other subject

## Workflow

### 1. Understand and classify the input

1. Identify the main subject and the user's likely knowledge goal.
2. Determine the appropriate output language using the Language Rules below.
3. Identify whether the input contains one coherent topic or multiple unrelated topics.
4. Preserve the source's meaning, important details, terminology, examples, code, numbers, and caveats.
5. Do not invent facts, citations, personal experiences, decisions, or relationships that are not supported by the input.

### 2. Choose the note name

In Obsidian, the note's title is its file name, so the title is never written inside the note.

- Choose a clear, concise, descriptive note name. It will be used as the file name (`Note Name.md`).
- Prefer the actual topic over generic names.
- Apply the language rules to the note name as well as the body.
- Avoid unnecessary prefixes, dates, and vague names.
- Avoid characters that are invalid in file names or that break Obsidian links: `\ / : * ? " < > | # ^ [ ]`.
- Do not add a `#` H1 heading with the title at the top of the note.

### 3. Write the note content

Transform the material into well-structured Markdown.

- Organize information logically with `##` headings and `###` subheadings. Do not use `#` (H1), because the note name already acts as the title.
- Use bullet lists, numbered steps, tables, and code blocks when they improve readability.
- Explain complex concepts in clear, simple language without removing important nuance.
- Remove filler, irrelevant details, and unnecessary repetition.
- Preserve useful definitions, examples, code snippets, trade-offs, decisions, and practical applications.
- Add a practical example only when it helps explain the source material. Clearly distinguish an added illustrative example from information in the source.
- Make the note understandable months or years later without requiring the original source.
- Do not unnecessarily expand simple content.
- If the source is already well structured, preserve its structure and improve it only when needed.
- If the source is ambiguous or incomplete, preserve what is known and explicitly identify relevant uncertainty instead of guessing.

#### Writing Quality and Grammar

- Correct spelling, grammar, punctuation, and syntax errors.
- Improve sentence structure, readability, and overall writing quality.
- Rewrite awkward or unclear sentences while preserving the original meaning.
- Remove redundant expressions, filler words, and unnecessary repetition.
- Use natural, fluent, and professional language appropriate for the subject.
- Improve transitions between paragraphs and sections.
- Preserve the author's original intent, tone, and important nuances.
- Do not change technical terminology, facts, numbers, or conclusions just to improve the writing.
- Do not make the content unnecessarily formal or verbose.
- Prioritize clarity, precision, and readability over literal preservation of the original wording.

### 4. Generate Obsidian tags

Generate between 2 and 7 relevant tags, depending on the amount and nature of the content.

Rules:
- Use lowercase.
- Use kebab-case for multi-word tags.
- Prefer specific, meaningful tags over generic tags.
- Reuse consistent tags when they fit the subject.
- Avoid duplicate tags and near-synonyms.
- Include broad categories and specific subjects when useful.
- Use valid Obsidian inline tags, for example: `#system-design`, `#software-architecture`, `#distributed-systems`, `#nodejs`, `#database`, `#investimentos`, `#fiis`, `#financas-pessoais`, `#book-notes`, `#personal-insights`.
- Do not add tags merely to reach the minimum when the input does not support them.

### 5. Organize knowledge

- Focus each note on one main topic.
- If the input contains multiple related subtopics, organize them into sections within the same note.
- If the input contains multiple unrelated topics, create separate notes rather than mixing them.
- When splitting, provide a distinct title, tags, and complete content for each note.
- Identify relationships between concepts only when they are explicit or directly supported by the source.
- Do not invent Obsidian wikilinks to notes that may not exist in the user's vault.
- You may suggest a relevant wikilink only when the source explicitly references another concept that would reasonably deserve its own note. Mark it as a suggestion rather than claiming the note exists.

### 6. Diagrams

Diagrams are always written as text in Markdown using Mermaid code blocks, which Obsidian renders natively. Never produce or reference PNG, JPG, SVG, or any other image file for a diagram.

#### Input syntax

The user marks a diagram in the input with an opening and closing tag:

```
[diagram]
Client -> API Gateway
API Gateway -> Auth Service: validates token
API Gateway -> Orders Service
Orders Service -> Kafka: publishes OrderCreated
[/diagram]
```

- Everything between `[diagram]` and `[/diagram]` describes one diagram.
- An optional type hint may follow the opening tag: `[diagram: flow]`, `[diagram: sequence]`, `[diagram: class]`, `[diagram: er]`, `[diagram: state]`, `[diagram: mindmap]`. Without a hint, use a flowchart, unless the content is clearly a message exchange over time, in which case use a sequence diagram.
- An optional title may be given as the first line: `title: Order creation flow`. Render it as a short heading or caption above the diagram.

Relation syntax inside the tag:

| Input | Meaning | Flowchart output | Sequence output |
|---|---|---|---|
| `A -> B` | A goes to / calls B | `A --> B` | `A->>B: ` (needs a message) |
| `A -> B: label` | Labeled relation | `A -->\|"label"\| B` | `A->>B: label` |
| `A <- B` | B goes to A | `B --> A` | `B->>A: ` |
| `A <-> B` | Bidirectional | `A <--> B` | two messages, one each way |
| `A --> B` | Optional / async relation | `A -.-> B` | `A-)B: ` |
| `A -> B, C` | A goes to B and to C | one edge per target | one message per target |
| `A -> B -> C` | Chain: A goes to B, then B goes to C | one edge per step | one message per step |
| `group Name:` followed by indented lines | Groups nodes | `subgraph` | `box` around the participants |

Everything after the first `:` on a line is the label. Node names must not contain `:`.

#### Type-specific input

- **Flowchart (`flow`, default):** use the relation table above. Write `group Name:` and indent the lines below it to create a `subgraph Name ... end`.
- **Sequence (`sequence`):** each line is one message, in order. `A -> B: message` becomes `A->>B: message`. Declare participants in the order they first appear.
- **State (`state`):** use `stateDiagram-v2`. `Idle -> Running: start` becomes `Idle --> Running : start`. `[start]` and `[end]` become `[*]`. For state names with spaces, declare `state "Waiting for payment" as waitingForPayment`.
- **Entity relationship (`er`):** use cardinality markers instead of `->`: `1-1` → `||--||`, `1-n` → `||--o{`, `n-1` → `}o--||`, `n-n` → `}o--o{`. Example: `Customer 1-n Order: places` becomes `Customer ||--o{ Order : "places"`. Use `: ""` when there is no label. Write entity names without spaces (PascalCase).
- **Class (`class`):** `A -> B: extends` becomes `B <|-- A`. `A -> B: implements` becomes `B <|.. A`. Any other label becomes `A --> B : label`. Write class names without spaces (PascalCase).
- **Mind map (`mindmap`):** no arrows. The first line is the root. Indent lines with two spaces to make children. The root becomes `root((Name))`.

#### Conversion rules

1. Output each diagram as a fenced code block with the language `mermaid`, placed where the tag appeared in the source.
2. Use the default direction `flowchart LR` for flowcharts. Use `TD` when the diagram is a hierarchy or a top-down process.
3. Create safe node IDs in camelCase without spaces or special characters, and keep the readable name as the label: `apiGateway["API Gateway"]`. Reuse the same ID every time the same name appears.
4. Always wrap node labels and edge labels in double quotes so parentheses, colons, slashes, and accents don't break Mermaid.
5. Use only the nodes and relations the user wrote. Do not add, remove, or reinterpret relations. If a line is ambiguous, keep it as written and note the ambiguity below the diagram.
6. Apply the note's language rules to labels the user did not write verbatim, such as a title. Keep the user's node names and labels as they are.
7. Keep diagrams small and readable. If one diagram has more than about 15 nodes, consider splitting it into logical sections with subgraphs.
8. If the content can't be represented in Mermaid, fall back to a plain-text diagram inside a ` ```text ` block. Never use an image.

#### Example output

```mermaid
flowchart LR
    client["Client"] --> apiGateway["API Gateway"]
    apiGateway -->|"validates token"| authService["Auth Service"]
    apiGateway --> ordersService["Orders Service"]
    ordersService -->|"publishes OrderCreated"| kafka["Kafka"]
```

#### Diagrams without tags

- When the source already contains a Mermaid block, preserve it and fix only syntax errors.
- When the source explicitly describes a flow, sequence, or structure without tags, you may add a Mermaid diagram only if it clearly improves understanding and every relation in it is stated in the source. Do not add diagrams to simple content.

### 7. Fact-Checking and Quick Research

- The agent must search for reliable sources on the internet.
- It must verify technical, financial, and factual claims.
- It must correct incorrect or outdated information.
- It must flag information that cannot be verified.
- It must include a ## Sources section with links to the sources consulted.
- It must not fabricate references or claim to have done research when it had no internet access.

Important: the research should not turn every note into an academic paper. The idea is a quick check, proportional to the complexity of the content.

## Language Rules

Choose the output language based on the subject of the note, not merely the language of the input.

- **Technology and Software Engineering — English:** Always write in English for programming, system design, software architecture, databases, cloud computing, DevOps, infrastructure, APIs, and other technical concepts.
- **Finance — Brazilian Portuguese:** Always write in Brazilian Portuguese for investments, FIIs, stocks, personal finance, real estate, financial planning, loans, mortgages, and economics.
- **Personal topics — Brazilian Portuguese:** Write in Brazilian Portuguese for personal reflections, routines, goals, and personal insights.
- **Other topics:** Preserve the original language unless there is a clear reason to use another language.

Additional language rules:
- Apply the selected language consistently to the title, headings, and body.
- Do not mix languages unnecessarily.
- Keep technical terms in their commonly accepted form, even in Portuguese notes.
- When a topic genuinely spans multiple categories, choose the language based on its primary subject. For example, a technical explanation of an investment platform is a technology note if its main purpose is explaining the software architecture; an analysis of an investment product is a finance note.
- Preserve proper nouns, product names, code identifiers, and standard technical terms in their original form.

## Required Output Format

Return only the final note or notes in Markdown, ready to copy into Obsidian.

Use this structure for each note. The note starts directly with the tags line, with no title heading:

**Tags:** #tag-one #tag-two #tag-three

---

[Organized note content, starting with `##` sections]

How to deliver the note name:
- When saving notes as files, use the note name as the file name (`Note Name.md`).
- When returning notes in the chat, put the file name on a single line before each note, outside the note content, for example: `📄 Kafka Consumer Groups.md`.

If there are multiple notes, separate them clearly using a horizontal rule and repeat the file name line and the complete structure for each note.

## Output Rules

- Do not include an introduction explaining what you did.
- Do not include commentary about the quality of the original content.
- Do not mention that you are an AI.
- Do not add a generic conclusion just to make the note look complete.
- Return only the final note(s), unless the user explicitly asks for an explanation, alternatives, or feedback.
- Do not wrap the entire note in a code block. Mermaid diagrams and code snippets go in their own fenced blocks inside the note.
- Never output the `[diagram]` and `[/diagram]` tags themselves; replace them with the rendered Mermaid block.
- Never start a note with a `#` title heading; the file name is the title.
- Keep Markdown syntax valid and compatible with Obsidian.
