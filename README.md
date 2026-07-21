# ai301-contribution-log

# Contribution 1: GraphQL schema descriptions leak WordPress implementation details (WP_Post properties, database columns, meta keys)


**Contribution Number:** 1

**Student:** Ankitha Beeram

**Issue:** [[GitHub issue link]  ](https://github.com/wp-graphql/wp-graphql/issues/4026#top)

**Status:** Phase I - Complete
---

## Why I Chose This Issue

I chose this issue because it's about a simple but important API design idea: people using an API shouldn't need to know how it's built underneath. Right now, WPGraphQL's schema descriptions mention internal details like PHP class names and database columns. This issue is about rewording those descriptions so they make sense to anyone, no WordPress knowledge required. In my own projects I've mostly used APIs, this is a chance to work on the other side and help shape one that hundreds of sites depend on. 

This issue is a strong starting point for my first contribution. The scope is clear and bounded and existing tests will verify my work, so I can focus on doing it well rather than worrying about breaking something. From this issue, I hope to learn three things: how a mature open source project keeps its code consistent, how a GraphQL schema looks from the builder's side instead of the user's side, and how the full contribution process works

---

## Understanding the Issue

### Problem Description

Some GraphQL schema `description` fields in WPGraphQL leak WordPress internals such as PHP class properties (`WP_Post->guid`), database table names (`"post_objects"`), and column names instead of describing what the field means to someone using the API.

### Expected Behavior

Descriptions should be self-describing and backend-agnostic. Someone reading the schema shouldn't need to know WordPress internals to understand a field.

### Current Behavior

Fields like `guid` and `slug` on `ContentNode` describe themselves in terms of PHP properties and database columns, e.g.: *"This currently matches the value stored in WP_Post->guid and the guid column in the 'post_objects' database table."*

### Affected Components

24 leaked descriptions across 6 files: `ContentNode.php`, `PostObjectCreate.php`, `TermObject.php`, `PostObject.php`, `User.php`, `Menu.php`, `Comment.php`.

---

## Reproduction Process

### Environment Setup

Cloned my fork, ran `npm install` and `npm run wp-env start` to bring up WordPress in Docker. Hit two missing-setup issues along the way: PHP dependencies weren't installed (fixed with `composer install` inside the container) and JS assets weren't built (fixed with `npm run -w @wpgraphql/wp-graphql build`). Also had to enable Public Introspection under GraphQL → Settings before GraphiQL would allow schema queries.

### Steps to Reproduce

1. Start the local environment and open GraphiQL.
2. Run: `{ __type(name: "ContentNode") { fields { name description } } }`
3. Observed result: the `guid` and `slug` fields return descriptions containing `WP_Post->guid`, `WP_Post->post_name`, and the `"post_objects"` table name — confirming the leak live.

### Reproduction Evidence

- **Commit showing reproduction:** https://github.com/AnkithaBeeram/wp-graphql/tree/fix/4026-schema-description-leaks
- **My findings:** The leak follows one repeated template (`WP_X->property` + `"column" in table`) across 24 fields in 6 files — a systematic pattern, not isolated typos.

---

## Solution Approach

### Analysis

Root cause: descriptions were written to explain where WordPress stores the value internally, rather than what the field means to a GraphQL consumer. Traced to 24 occurrences across the 6 files above, all following the same template.

### Proposed Solution

Reword each leaked description to explain the field's meaning and purpose, removing any WP class property, table name, or column name. Scope this pass to removing implementation plumbing only (Level 1); leave broader WordPress-vocabulary rewording (Level 2) as a follow-up.

### Implementation Plan

**Understand:** Schema descriptions leak WordPress internals instead of describing fields in user-facing terms.

**Match:** PR #4025 already reworded a similar leaky description into user-facing language — I'll follow that same style.

**Plan:**
1. Rewrite the 24 leaked `description` strings in the 6 identified files.
2. Regenerate schema snapshot test fixtures to match the new text.
3. Run the test suite and confirm nothing else broke.

**Implement:** Branch: https://github.com/AnkithaBeeram/wp-graphql/tree/fix/4026-schema-description-leaks

**Review:** Follows `CLAUDE.md`'s schema-description convention; PR scoped narrowly (Level 1 only); Conventional Commit title per project guidelines.

**Evaluate:** Re-run WPUnit tests with updated snapshots; manually re-run the introspection query and confirm the leaked text is gone; run PHPCS to confirm coding standards are met.

---

