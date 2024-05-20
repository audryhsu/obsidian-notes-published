# obsidian-notes-published
Repository containing published versions of my personal Obsidian notes and drawings.

# How I publish notes
- I use the [Obsidian Github Publisher](https://github.com/ObsidianPublisher/obsidian-github-publisher) plugin to push notes with the `publish: true` tag to this repository.
- Content settings: enable internal links, wikilinks to MDlinks
- Attachment & embed settings: enable send linked files, transfer attachments, transfer embedded notes, and allow `svg` under "Allow extensions not natively supported by Obsidian"

## Publishing and embedded Excalidraw drawings
- To properly render embedded drawings created with [Excalidraw](https://github.com/zsviczian/obsidian-excalidraw-plugin), embed the auto-created .SVG version of drawings instead of native .excalidraw files (this can be enabled in Excalidraw settings)
  - Enable setting to sync changes between .excalidraw drawings and SVG/PNG exports as long as they have the same name so any changes are captured and can be re-published.
- Embed drawings as Obsidian wiki links (`.excalidraw` version) for a better native app experience while using Obsidian. Just make sure that the Github Publish is configured to transform wikilinks to markdown links.

# Hosting notes
TODO: consider using a static site generator like Jekyl and host it on github pages, git books, or mkdocs. 
