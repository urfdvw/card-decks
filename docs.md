## What is a card

> definition

a piece of knowledge that can fit a single page like
- how-to guide of a small task
- code snippet
- where to find a doc
- simple fact
- a piece of informative conversation
- a reusable check list

---

## Syntax of cards

> grammar, definition

- A card deck is a markdown file
- Cards in a deck are separated by `---`
- Include markdown titles in a card. Usually start with `## `
- Contents in line starts with `> ` are considered as tags
    - tags are only designed for search, adding key words that are missing in the title or text

Note: Check the raw markdown of this file for more example

---

## How to write search prompt

> search, keywords, tags

- Keywords are separated by `,` and newline.
    - `,` means OR.
    - Newline means AND.
- Put `#` in front of keywords to only match text in the titles and tags.
- Put `@` in front of keywords to only match text in the file path.
- Keywords need to be over 2 letters. Ignored other wise.

---

## How to create your card decks

> markdown, notes, github page

- Fork this repo
- Create/edit markdown files in the repo as card decks
    - You can use `readme.md` as your default card deck or create other decks
- Publish the repo as github page
- The deck link are the published github page asset link

Note: assets are published slower than the html pages, usually by 1 hour.
As a result, the link to the card deck might not work when your github page first available.

---

## How to edit and search cards locally

> localhost, local server

The following steps is the local workflow to edit and search your notes locally before publish.
- Clone your clone of this repo locally
    - Which contains your notes
- Open the repo with VScode
- To edit, you can edit the notes in vscode
- To search
    - Navigate to `index.html`
    - Install vscode extension `Live Server`
    - In the right side of bottom toolbar, click on `Go Live`
    - And the Card Deck Web UI will show up in the browser.

---

## How to search from multiple card decks

> markdown, notes, team

- Paste the links to all card decks to the `Source` text area
    - Separated by newlines
- For decks in the local repo, use relative path
    - e.g. `docs.md`
- For decks in other repos, use the full url link

Note: images linked with relative path in external card decks are not able to be displayed at the moment.

---

## How to share card

> link, url

- url will include prompt and deck source parameters.
- When a card is found in search result, simply copy the current url and share.