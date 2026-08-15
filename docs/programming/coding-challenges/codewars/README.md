# Codewars

This page records Codewars kata after they have been completed as deliberate practice.

## Personal Completion Log

No independently completed kata have been recorded yet. The worked example below demonstrates the note format but does not count as a completed session.

## Worked Format Example

This is a format demonstration, not a claim that the kata is in the personal completion log.

### Vowel Count

- **Kata:** [Vowel Count](https://www.codewars.com/kata/54ff3102c1bad923760001f3)
- **Rank:** 7 kyu
- **Language:** JavaScript
- **Topics:** strings, sets, iteration

#### Problem Summary

Count the lowercase English vowels in an input string.

#### Approach

Store the accepted vowels in a set and inspect each Unicode code point produced by string iteration. Increment the total when the set contains the character. The kata's stated contract is lowercase input; a production text utility would need an explicit decision about uppercase letters, accents, normalisation, and what “vowel” means for other languages.

#### Complexity

- **Time:** `O(n)` for `n` iterated characters.
- **Space:** `O(1)` because the set has a fixed size.

#### Solution

```javascript
export function countVowels(text) {
  const vowels = new Set(["a", "e", "i", "o", "u"]);
  let count = 0;

  for (const character of text) {
    if (vowels.has(character)) {
      count += 1;
    }
  }

  return count;
}
```

#### Lessons

- Solve the stated contract before generalising it.
- A set communicates membership more directly than nested conditionals.
- Include empty strings, repeated vowels, and strings without vowels in tests.

## Kata Entry Template

```markdown
## Kata Title

- **Kata:** [Original kata](https://www.codewars.com/kata/example)
- **Rank:** 8 kyu through 1 kyu
- **Language:** Java, Kotlin, Python, or another language
- **Topics:** Strings, collections, mathematics, algorithms, or other topics

### Problem Summary

Describe the task and important constraints in your own words.

### Approach

Explain the reasoning, alternatives considered, and why the selected approach works.

### Complexity

- **Time:** `O(...)`
- **Space:** `O(...)`

### Solution

Add the implementation in a language-labelled code block.

### Lessons

Record edge cases, mistakes, alternative solutions, and reusable language features.
```

Return to [Coding Challenges](../README.md).
