# Wikipedia Search

A short browser-automation demo: open Wikipedia, search for "automation", open the article and read its title.

## What happens

1. Opens `https://www.wikipedia.org/` in the automated browser.
2. Clicks the English link, then the search box.
3. Types `automation` and presses Enter.
4. Reads the article heading (`#firstHeading`) and ends. A screenshot of the final page is captured with the run.

## Before you run it

- **Needs internet access.**
- A browser window opens and is controlled by DeskStride while it runs. Leave it alone until it finishes.
- Nothing is changed on your machine.

## Make it yours

- Change the text in the Type node to search for anything else.
- Add more steps after **Extract Text**, such as saving the title to a file or a variable.
- Selectors like `#searchInput` come from Wikipedia's page. If the site changes, update them the same way you would for any other site.
