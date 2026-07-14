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

