# ai-photo-enhancer

An early **AI photo enhancer** experiment (static HTML page).

Static single-page demo — open `index.html` in a browser to try it.

## How it works

Upload a photo, adjust the enhancement, and download the result — everything runs in the browser, no upload to a server.

## Architecture

```mermaid
flowchart LR
    User([User]) --> Upload["Upload photo<br/>FileReader"]
    Upload --> Canvas["Canvas processing<br/>getImageData → pixel filters"]
    Canvas --> Filters["Enhancements<br/>brightness · contrast · sharpen"]
    Filters --> Preview["Before / after preview<br/>side-by-side canvas"]
    Preview --> DL["Download enhanced image<br/>toDataURL"]
```
