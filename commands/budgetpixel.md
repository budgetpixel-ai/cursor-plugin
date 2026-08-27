---
name: budgetpixel
description: Generate, edit, upscale, and publish AI images, video, music, and sound effects using the BudgetPixel MCP tools.
---

# /budgetpixel

Natural-language entrypoint for BudgetPixel. Interpret the request, keep the user's
constraints, and treat the live MCP tool descriptions as the source of truth — the model
catalog changes as BudgetPixel adds models, so never assume a fixed list.

## Usage

```
/budgetpixel <natural language request>
```

## Examples

```
/budgetpixel A cinematic poster of a lighthouse in a storm, 16:9
/budgetpixel Take the image I just made and turn it into a 5s clip, 480p
/budgetpixel Write a 30-second lo-fi track for a coding video
/budgetpixel Rain on a tin roof with distant thunder, 8 seconds
/budgetpixel Upscale that render to 16 MP
/budgetpixel What models can do text inside an image?
/budgetpixel How many credits do I have left?
```

## Routing

- **Still image** — `generate_image`. Text-to-image from a prompt, or editing /
  image-to-image / multi-reference by passing `input_images`. To edit something just
  generated, pass its URL. Input support is per-model; check `max_input_images` via
  `list_models` before assuming a model takes references.
- **Video** — `generate_video`. Text-to-video, image-to-video (`image` start frame plus
  optional `end_image`), reference-to-video (`reference_images`), or video-to-video
  (`video`). See the cost rules below before calling it.
- **Songs and background music** — `generate_music`.
- **Foley, ambience, UI sounds, impacts, transitions** — `generate_sound_effect`. This is
  not music; route musical requests to `generate_music`. Pass a `video` URL to get effects
  synced to a clip's visible events.
- **Higher resolution** — `upscale_image` for stills, `upscale_video` for clips.
- **Which model should I use / what does this cost** — `list_models`. It returns live
  prices, role hints, and per-model input limits.
- **Recent work** — `get_generation_history` (pass `type` for video, music, or
  sound_effect). Result URLs are reusable as inputs to another tool.
- **Credits and plan** — `get_credit_balance`.
- **Publishing to the public feed** — `create_social_post`, and only on an explicit request.

## Cost discipline

Video is billed **per second and by resolution**, and it is the one place a careless call
gets expensive. On the default model a 5-second 720p clip is roughly 1,650 credits and
1080p roughly 3,750.

- Default to **480p** unless the user asks for more.
- State the estimated cost and confirm before generating 1080p, anything over 5 seconds,
  or a job with several reference clips.
- Image upscales are priced by target megapixels; video upscales by input second, with 4K
  costing four times 1080p. Quote 4K before running it.
- Failed generations are never charged. Charges land on success only.

## Async results

Every generate tool returns a job id rather than the finished media.

| Tool | Job id | Poll with | Typical wait |
|---|---|---|---|
| `generate_image` | numeric | `check_generation_status` | seconds |
| `generate_video` | uuid | `check_generation_status` | minutes |
| `generate_music` | numeric | `check_generation_status` with `type: "music"` | a few minutes |
| `generate_sound_effect` | numeric | `check_generation_status` with `type: "sound_effect"` | ~15s text, minutes for video |
| `upscale_image` | — | returns the result directly | seconds |
| `upscale_video` | numeric | call `upscale_video` again with just `job_id` | minutes |

Music and sound-effect ids are numeric like image ids, so the `type` argument is
**required** for those two — without it the id is read as an image job.

Poll every few seconds for images and text sound effects, every 15–30 seconds for video
and music. Display finished images inline **and** share the link, since some chat UIs drop
inline images. For video and audio always share the link — Cursor cannot embed players.

## Media inputs

- A public http(s) URL can be passed straight to a generate tool.
- A **local file must go through `upload_media` first** — read it, pass the base64, and use
  the returned short-lived URL. Video and audio inputs on `generate_video` accept URLs
  only, so local media has no other path in.
- Preserve every reference the user supplies and the role they gave it. A start frame is
  not a style reference.

## Publishing

`create_social_post` publishes to the user's **public** BudgetPixel feed, labeled "via MCP".
Never call it proactively — only when the user explicitly asks to post or share. It costs
credits per post and is subject to an hourly cap, and posts are moderated after publishing.

## Account requirements

The account-bound tools — generation, uploads, upscales, posting, history, balance —
require a Premium, Pro, or Ultra plan. Free-plan users can call `list_models`; the others
return an upgrade notice. If a call fails for credits or plan reasons, relay the notice
rather than retrying the same call.

Do not invent model ids, parameters, or prices when `list_models` can supply them. Do not
retry an unchanged call that already failed — read the error and adjust.
