# BudgetPixel for Cursor

Generate AI images, video, music, and sound effects from inside Cursor, using the
[BudgetPixel](https://budgetpixel.com) MCP server.

Nothing to install and no API keys to manage — you connect once, sign in with Google, and
your BudgetPixel plan credits and discounts apply automatically.

## Install

```
/add-plugin budgetpixel
```

Or add the MCP server on its own:

```bash
claude mcp add budgetpixel --transport http https://mcp.budgetpixel.com/mcp
```

## Usage

```
/budgetpixel A cinematic poster of a lighthouse in a storm, 16:9
/budgetpixel Turn that into a 5 second clip at 480p
/budgetpixel Write a 30 second lo-fi track for a coding video
/budgetpixel Upscale the render to 16 MP
```

## What's included

| Tool | What it does |
|---|---|
| `generate_image` | Text-to-image, editing, image-to-image, and multi-reference generation |
| `generate_video` | Text-to-video, image-to-video (start plus optional end frame), reference-to-video, and video-to-video editing |
| `generate_music` | Full songs from a prompt and/or your own lyrics, or instrumentals |
| `generate_sound_effect` | Foley, ambience, UI sounds, impacts, and transitions — optionally synced to a video's events |
| `upscale_image` | Higher resolution in seconds, or creative detail enhancement steered by a prompt |
| `upscale_video` | 1080p or 4K at 30 or 60 fps via Topaz Labs, inputs up to 20 seconds |
| `list_models` | The live model catalog with prices, role hints, and per-model input limits |
| `upload_media` | Upload a local file and get a URL to use as a generation input |
| `check_generation_status` | Poll a running job and collect the finished media |
| `get_generation_history` | Your recent generations, reusable as inputs |
| `get_credit_balance` | Remaining plan credits |
| `create_social_post` | Publish a finished generation to the BudgetPixel community feed |

## Requirements

A BudgetPixel account on any paid plan (Starter, Basic, Premium, Pro or Ultra). Free-plan users can browse the
model catalog; the generation tools return an upgrade notice.

Video is priced per second and by resolution, so `/budgetpixel` will quote the cost and ask
before running anything expensive.

## Links

- [budgetpixel.com/mcp](https://budgetpixel.com/mcp) — setup guides for Claude, ChatGPT, Cursor, and VS Code
- [budgetpixel.com](https://budgetpixel.com) — the platform
- [Pricing](https://budgetpixel.com/pricing)

## Support

[support@budgetpixel.com](mailto:support@budgetpixel.com)

## License

MIT — see [LICENSE](LICENSE).
