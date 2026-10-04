# Sample Bludit Blog Posts

Eight ready-to-import sample posts for Bludit, covering a mix of content formats and HTML stylings so you can exercise every layout your theme needs to support: code blocks, tables, blockquotes, lists, side-by-side image layouts, an image gallery grid, an embedded `<video>`, and an embedded `<audio>` player.

## Why raw HTML, and why no front matter in the post files

Bludit stores each post as **two separate things**:

1. `bl-content/pages/<slug>/index.txt` — the raw post body only (HTML here; Bludit also accepts Markdown if your install has that parser enabled). There is no front matter in this file.
2. `bl-content/databases/pages.php` — a single JSON database (prefixed with a required PHP guard line) holding every post's metadata: title, description, tags, category, cover image, dates, and more.

Because of that, this sample content ships as actual Bludit page folders plus ready-to-merge database JSON, rather than as a fenced-code "cheat sheet." You can drop it straight into a Bludit install and see it render immediately.

## What's included

```
sample-content/bl-content/
├── pages/
│   ├── behind-the-scenes-building-our-new-studio/index.txt   (video post)
│   ├── a-quiet-morning-in-the-mountains/index.txt            (image post)
│   ├── new-episode-design-systems-at-scale/index.txt         (audio post)
│   ├── studio-snapshots-a-visual-gallery/index.txt           (gallery post)
│   ├── just-shipped-a-major-update/index.txt                 (status post)
│   ├── getting-started-with-the-bludit-theme-api/index.txt   (standard post)
│   ├── ten-productivity-tools-we-cant-live-without/index.txt (standard post)
│   └── the-future-of-remote-work-trends-to-watch/index.txt   (standard post)
└── databases/
    ├── pages.php       (post metadata — title, tags, category, cover image, custom.format, etc.)
    ├── categories.php  (7 categories: video, photography, podcast, updates, development, productivity, business)
    └── tags.php        (21 tags used across the 8 posts)
```

All featured/cover images and inline images are hotlinked directly from `images.unsplash.com` — Bludit accepts a plain external URL for `coverImage`, so no local image hosting is required.

## How to install into a real Bludit site

1. Copy each folder under `sample-content/bl-content/pages/` into your site's `bl-content/pages/` directory.
2. Merge the contents of `sample-content/bl-content/databases/pages.php`, `categories.php`, and `tags.php` into your site's corresponding files at `bl-content/databases/`. If those database files already contain your own content, merge the JSON objects (these sample keys/slugs are namespaced by their own slugs/tag/category names, so they're unlikely to collide) rather than overwriting the whole file. Keep the `<?php defined('BLUDIT') or die('Bludit CMS.'); ?>` guard line as the first line of each file.
3. Clear Bludit's cache if your install uses one, then reload the admin panel — the 8 posts, their categories, and their tags should all appear.

## Post formats: there's no native concept in Bludit, here's a better way

Bludit's core has no built-in idea of "video post" / "gallery post" / "status post" the way WordPress or Tumblr do. Rather than inventing an unsupported convention (like a magic tag or a title prefix), each sample post sets a **custom field** instead, since `custom` fields are already a first-class part of Bludit's page schema:

```json
"custom": {
    "format": {
        "label": "Post format",
        "type": "select",
        "value": "video"
    }
}
```

Values used across the sample set: `video`, `image`, `audio`, `gallery`, `status`, `standard`.

To make this editable from the admin "New/Edit content" screen (instead of only being set by hand in the database), register the field in your theme's `content-fields.php`:

```php
<?php defined('BLUDIT') or die('Bludit CMS.');

return [
    'format' => [
        'type' => 'select',
        'label' => 'Post format',
        'options' => [
            'standard' => 'Standard',
            'video' => 'Video',
            'image' => 'Image',
            'audio' => 'Audio',
            'gallery' => 'Gallery',
            'status' => 'Status'
        ],
        'value' => 'standard'
    ]
];
```

Then in your templates, branch on it with `$page->custom('format')`:

```php
<?php switch ($page->custom('format')) {
    case 'video':
        // render video template partial
        break;
    case 'gallery':
        // render gallery grid partial
        break;
    // ...
} ?>
```

This keeps the "post format" idea fully inside Bludit's supported extension points, is editable from the admin UI once registered, and degrades gracefully (an unrecognized or missing value can just fall back to your standard post template).

## Content/styling reference

| Post | Format | Category | Styling used |
|---|---|---|---|
| Behind the Scenes: Building Our New Studio | video | video | `<video>` embed, blockquote, unordered list |
| A Quiet Morning in the Mountains | image | photography | full-width figure, side-by-side figure layout, blockquote |
| New Episode: Design Systems at Scale | audio | podcast | `<audio>` embed, table (timestamps), unordered list (show notes) |
| Studio Snapshots: A Visual Gallery | gallery | photography | CSS grid gallery of 5 images |
| Just Shipped: v2.4 | status | updates | short single paragraph |
| Getting Started with the Bludit Theme API | standard | development | ordered list, PHP code block, table (API reference) |
| 10 Productivity Tools We Can't Live Without | standard | productivity | blockquote, unordered list, comparison table |
| The Future of Remote Work: Trends to Watch | standard | business | side-by-side image layout, ordered list, stats table, blockquote |
