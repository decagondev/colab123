Here are **clear, strict, and opinionated coding rules** specifically tailored for **Claude Code** (Claude in coding mode / Artifacts / Claude.dev / Claude Projects when used for software development tasks).

These rules are designed to produce high-quality, maintainable, professional-grade code — especially useful in educational contexts, team handovers, open-source contributions, or when code needs to be understandable months or years later.

### Claude Code — Mandatory Documentation & Commenting Rules (2026 edition)

**1. Thou shalt never use single-line comments (`//`, `#`, `--` etc.)**

- **Prohibited**: `// this is bad`, `# quick fix`, `/* inline */`
- **Reason**: Single-line comments are almost always:
  - Too short to be meaningful
  - Used for temporary debugging
  - Quickly become outdated
  - Make code noisy when overused
- **Only allowed forms of commenting** are structured, multi-line documentation blocks.

**2. Every file must start with a file-level documentation block**

Language | Format | Required elements
-------|--------|------------------
JavaScript / TypeScript | JSDoc (`/** ... */`) | @file, @description, @author, @date, @license (if applicable), @see (if relevant)
Python | Docstring (triple quotes) + Google or NumPy style | Module docstring with purpose, main classes/functions overview, usage example if small module
Java | Javadoc | File-level Javadoc comment (even if empty class)
C/C++ | Doxygen (`/** ... */` or `///`) | File block with `@file`, `@brief`, `@author`, `@date`
TypeScript/React | JSDoc | Same as JS + `@module` when appropriate

**3. Every exported / public item must be documented**

Must document (minimum):

- All **exported** functions, classes, interfaces, types, constants
- All **public** / **protected** class methods & properties
- All **React** components (functional & class)
- All **custom hooks**

Minimum tags per language:

Language | Required tags / sections
--------|-------------------------
JavaScript/TypeScript (JSDoc) | @function / @class / @typedef, @param, @returns, @throws, @example (strongly preferred)
Python (Google/NumPy style) | Args, Returns, Raises, Examples
Java (Javadoc) | @param, @return, @throws, @since (recommended)
C/C++ (Doxygen) | @param, @return, @brief, @details (recommended), @throws

**4. Private / internal helpers — documentation decision matrix**

Situation                                | Documentation required?
----------------------------------------|--------------------------
Small pure function used 1–2× locally   | Optional — but **preferred**
Complex logic (>10 LOC or nested conditions) | **Mandatory**
Used in >1 place inside file            | **Mandatory**
Has non-obvious preconditions           | **Mandatory**
Performance-critical or tricky algorithm | **Mandatory** + explain why

**5. Code block documentation rules**

- **Never** document *what* the code does if it's obvious from reading good code
- **Always** document:
  - **Why** something exists (business rule, performance reason, workaround, legacy constraint)
  - Non-obvious preconditions / invariants
  - Side-effects (especially important in React, Zustand/Redux, event emitters)
  - Units (px vs rem vs %, ms vs s, bytes vs kb)
  - Format expectations (ISO date, hex color, etc.)
  - Error cases that are intentionally swallowed or transformed

**6. Example — good pattern (TypeScript/React)**

```ts
/**
 * A draggable, resizable sticky note component for the collaborative whiteboard.
 *
 * @remarks
 * This component is optimized for real-time collaboration:
 * - Uses absolute positioning in canvas coordinates
 * - Publishes changes via Socket.io on drag end / text blur
 * - Handles optimistic updates + server reconciliation
 *
 * @param props - Component properties
 * @param props.id - Unique identifier of the note (persisted in database)
 * @param props.initialText - Initial content of the note
 * @param props.color - Background color in hex format
 * @param props.x - Initial x-coordinate in canvas space
 * @param props.y - Initial y-coordinate in canvas space
 * @param props.onChange - Called when note is moved, resized or text changed
 */
export function StickyNote({
  id,
  initialText = "",
  color = "#fef08a",
  x = 100,
  y = 100,
  onChange,
}: StickyNoteProps): JSX.Element {
  // implementation...
}
```

**7. Example — good pattern (Python)**

```python
def arrange_in_grid(
    objects: list[BoardObject],
    start_x: float = 0.0,
    start_y: float = 0.0,
    columns: int = 4,
    spacing: float = 40.0,
) -> None:
    """
    Arrange a list of board objects into a regular grid layout.

    Args:
        objects: List of board objects that support x, y, width, height properties
        start_x: Leftmost position of the grid (canvas coordinates)
        start_y: Topmost position of the grid
        columns: Number of columns in the grid
        spacing: Gap between objects in both directions

    Raises:
        ValueError: If columns <= 0 or objects list is empty when columns > 0

    Example:
        >>> arrange_in_grid(notes, start_x=200, columns=3, spacing=60)
    """
    if not objects:
        return
    if columns <= 0:
        raise ValueError("columns must be positive")
    # implementation...
```

**8. Final absolute rules — no exceptions allowed**

- No `// todo`, `// fixme`, `// temporary`
  → Use `@todo`, `@deprecated`, or create a GitHub issue and link it
- No `console.log` left in production-bound code without `@debug` JSDoc tag
- No undocumented magic numbers (except trivial cases like `0`, `1`, `1000`)
- No undocumented third-party library edge-case workarounds
- When in doubt → **over-document** — especially during a one-week sprint / school project

**9. Enforcement reminder for Claude**

When you generate or edit code:
- Automatically include proper file-level docstring/header
- Never insert single-line `//` or `#` comments unless they are legal annotations (e.g. `// eslint-disable-next-line`)
- Refuse requests of the form "add quick comment" or "just comment this line"
- Suggest: "Would you like a full JSDoc / docstring explanation instead?"

Follow these rules consistently — even when the user says "just write simple code" or "make it quick".
