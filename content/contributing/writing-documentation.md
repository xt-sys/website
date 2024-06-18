+++
title = 'Writing Documentation'
date = 2024-06-17T18:08:34+02:00
+++
Our documentation is integrated with this website and it is stored in GIT repository. This website is powered by the Hugo
Static Site Generator. All pages are created as simple Markdown files and everyone is free to submit Pull Requests to
enhance the website. This article will help you better understand the proper guidelines for submitting documentation.

### Guidelines
 * **Use standard English**: Use standard United States (U.S.) English throughout all technical publications.
 * **Write in active-voice**: Active voice sentences clarify the performer of an action and are easier to understand than
   passive-voice sentences.
 * **Use present simple tense**: Users read documentation to perform tasks or gather information. For users, these
   activities take place in their present, so the present tense is appropriate in most cases.
 * **Write in second person**: Users are more engaged with documentation when you use second person (that is, you address
   the user as "you").
 * **Use appropriate mood**: For procedures, use imperative mood.
 * **Avoid ambiguous titles**: Each title should include a clear description of the page’s subject.
 * **Write objectively**: Do not use humor, jargon, exclamation marks, idioms, metaphors, and other colloquialisms.
 * **Write positively**: Write in a positive tone. Positive sentences improve readability.
 * **Avoid personification**: Avoid the adverbs such as "probably", "hopefully", "basically", and so on.
 * **Do not use contractions**: Generally, do not contract the words.
 * **Use spelling and grammar checking tools**: Run text through spelling and grammar checking tools, if available.

### Testing Documentation
 1. [Download](https://gohugo.io/) Hugo v0.125.7 or newer.
 2. Open a shell in the root of your repository and use below command to start server
    ```
    hugo server -DF
    ```
 3. Open `http://localhost:1313/` in your browser to access the website and check the results.

### Submitting Patches
We welcome patches for both documentation and the website. We use the GNU Free Documentation License 1.3 or later for all
documentation and website content. Contributions using other licenses are unlikely to be accepted. Patches should be
contributed via our [Gitea instance](https://git.codingworkshop.eu.org/xt-sys/website).
