# DCI Kenya Data

Source-linked public reports from DCI Kenya, the National Police Service, and other contextual sources.

Five JSONL datasets connect post text with Cloudinary image links, OCR, face detections, and number-plate observations.
Use the files to inspect source material, test extraction methods, or build research tools.

This is an independent research archive, not an official police publication.

## Datasets

Snapshot: **26 October 2026**. The five files total **8.56 MB**.

| Dataset | Records | Contents | Download |
|---|---:|---|---|
| [Posts](posts/posts.jsonl) | 2,814 | Source text, account, publication date, source URL, reply/quote IDs, and image IDs | [JSONL](https://raw.githubusercontent.com/kipropbrian/DCIKenyaData/main/posts/posts.jsonl) |
| [Images](images/images.jsonl) | 4,712 | Cloudinary delivery URLs, available original attachment URLs, and source-post references | [JSONL](https://raw.githubusercontent.com/kipropbrian/DCIKenyaData/main/images/images.jsonl) |
| [OCR](ocr/ocr.jsonl) | 1,384 | Extracted text and normalized text, linked to source posts | [JSONL](https://raw.githubusercontent.com/kipropbrian/DCIKenyaData/main/ocr/ocr.jsonl) |
| [Faces](faces/faces.jsonl) | 4,887 detections | Bounding boxes, detection scores, and quality assessments, linked to images | [JSONL](https://raw.githubusercontent.com/kipropbrian/DCIKenyaData/main/faces/faces.jsonl) |
| [Number plates](numberplates/numberplates.jsonl) | 538 observations | Plate readings, extraction methods, source references, and review status | [JSONL](https://raw.githubusercontent.com/kipropbrian/DCIKenyaData/main/numberplates/numberplates.jsonl) |

Images stay on Cloudinary. This repository contains links and metadata, not image files.
Face counts describe detections, not unique people. Plate counts describe observations, not unique vehicles.

```text
posts/posts.jsonl
images/images.jsonl
ocr/ocr.jsonl
faces/faces.jsonl
numberplates/numberplates.jsonl
```

## Sources and coverage

The current snapshot includes:

- **2,518 posts** from [DCI Kenya](https://x.com/DCI_Kenya).
- **277 posts** from the [National Police Service](https://x.com/NPSOfficial_KE).
- **19 contextual posts** from other accounts, including ODPP Kenya and news sources.

Publication dates range from **19 May 2019 to 23 October 2026**.
Coverage is selective. The archive is not a complete record of police activity or crime in Kenya.
Only posts marked relevant by the processing pipeline enter these JSONL files.

Each post retains its source account and original post URL.
Historical quote wrappers around numeric post IDs are removed, and duplicate public post IDs are consolidated.
Post IDs remain strings to preserve their full precision.

## How the files connect

| Field | Meaning |
|---|---|
| `post_id` | Original X post ID, stored as a string |
| `source_account` | Account that publishes the source post |
| `source_url` | Direct link to the source post |
| `image_id` | Stable public image identifier |
| `image_ids` | Image references associated with a record; empty when an exact association is unavailable |
| `source_posts` | Source references containing a post ID, account, and source URL |
| `cloudinary_url` | Public image delivery URL |
| `face_id` | Public identifier for a detection; it does not identify a person |
| `bbox` | Face coordinates in pixels: `left`, `top`, `width`, and `height` |
| `observation_id` | Public identifier for a plate observation |

Posts list their linked image IDs. Image records link back to source posts.
Face detections reference `image_id` and the associated source post IDs.

Historical OCR is aggregated at **post level**. Its `scope` is `post`, and its `image_ids` list is empty.
The export does not guess which individual image supplies each text fragment.

Plate observations use two methods:

- `llm_text_extraction`: a candidate plate extracted from post text. `present_in_source_text` records whether its normalized characters occur in that text.
- `stored_vehicle_reading`: a plate held in the application vehicle record, linked to its source posts.

Both methods remain **unreviewed**. Conflicting readings stay separate.
Plate records do not claim an exact image association when that evidence is unavailable.

## Read the data

JSONL contains one UTF-8 JSON object per line. Python can read a file without extra packages:

```python
import json
from urllib.request import urlopen

url = "https://raw.githubusercontent.com/kipropbrian/DCIKenyaData/main/posts/posts.jsonl"
with urlopen(url) as response:
    for line in response:
        post = json.loads(line)
        print(post["post_id"], post["source_account"], post["source_url"])
```

## Quality and privacy

Source statements and machine outputs can contain errors.
Scores do not establish identity, vehicle ownership, or guilt.
The records describe collected reporting; they do not measure crime rates.

- Missing original attachment URLs remain `null`; Cloudinary links and source-post references remain available.
- Face records include usable, low-quality, and rejected detections. Use `quality_status` when selecting records.
- The files exclude face embeddings, identity assignments, private case material, credentials, internal logs, and infrastructure details.
- Source posts still contain third-party names and allegations. The dataset is not anonymized.

## Updates

Weekly file updates are planned. Automatic GitHub publication is **not enabled yet**.

New records append to the same five files. Corrections update records with existing IDs.
Git history records each published change. The filenames do not change with the year or update date.

## Legacy CSV

The original [November 2024 CSV export](old/TwExportly_DCI_Kenya_tweets_2024_11_04.csv) remains available.
It can overlap with the JSONL corpus. Do not combine their row counts without checking duplicate post IDs.

## Rights and attribution

Attribution stays linked to each original source.
No repository-wide data licence is specified. Publication does not assign a new licence to third-party posts or images.
