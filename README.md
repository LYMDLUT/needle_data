# VisualNeedle 300 (EN)

A visual "needle-in-haystack" benchmark for VLMs: each task asks about a tiny detail in a relatively large image. The model must locate and reason over a small region (median ~0.1% of image area) to answer correctly.

## Stats

- **Size**: 300 samples
- **Language**: English questions, answers may be in English or Chinese (when answer is text in the image)
- **Image resolution**: width 599–7952 px, height 648–8223 px (median 1440×1706)
- **Bbox size**: 0.012% – 4.44% of image area (median **0.10%**)

### Categories

| Count | Category | Description |
|----:|---|---|
| 73 | OCR Recognition | read text from a small region |
| 73 | Color Recognition | identify the color of a small object |
| 64 | Entity Recognition | identify a small / partial object |
| 59 | Spatial Relationship | reason about position / direction |
| 31 | Occluded Object Recognition | identify a partially occluded object |

## Schema

Each line in `visualneedle_300en.jsonl` is a JSON object:

| Field | Type | Description |
|---|---|---|
| `id` | string | unique sample id |
| `category` | string | one of the 5 categories above |
| `question` | string | English question |
| `answer` | string | ground-truth answer |
| `alt_answers` | list of string | other answers that also count as correct (empty for most samples) |
| `neg_answers` | list of string | near-miss answers that must be judged wrong (empty for most samples) |
| `image_path` | string | image file in this repo |
| `image_url` | string | the same image on COS; empty when the COS copy is outdated (see below) |
| `image_bbox_path` | string | same image with the answer bbox drawn (red), for visualization only |
| `image_bbox_url` | string | COS copy of `image_bbox_path`; empty when outdated |
| `bbox` | `[x1, y1, x2, y2]` | answer region, **xyxy absolute pixel coords** |

`image_path` is the reference image for evaluation. Use it instead of `image_url`: 94 COS images predate the current masks, so their URLs are left empty rather than pointing at an older version.

When grading, a prediction matching `answer` or any entry of `alt_answers` is correct; a prediction matching an entry of `neg_answers` is wrong.

## Example

```json
{
  "id": "TPROMPT9ee217718764",
  "category": "OCR Recognition",
  "question": "In the picture, what is the second character to the right of '8元'?",
  "answer": "辣",
  "alt_answers": [],
  "neg_answers": [],
  "image_path": "images/TPROMPT9ee217718764.png",
  "image_url": "https://visualneedle2-1311238981.cos.ap-guangzhou.myqcloud.com/datasets/visualneedle_mask_face_300en/images/TPROMPT9ee217718764.png",
  "image_bbox_path": "images_bbox/TPROMPT9ee217718764.png",
  "image_bbox_url": "https://visualneedle2-1311238981.cos.ap-guangzhou.myqcloud.com/datasets/visualneedle_mask_face_300en/images_bbox/TPROMPT9ee217718764.png",
  "bbox": [1128, 1158, 1193, 1187]
}
```

## Coordinate convention

`bbox` is **xyxy absolute pixel** in the original image's coordinate system: `(x1, y1)` is the top-left corner, `(x2, y2)` is the bottom-right (exclusive). To draw with PIL:

```python
from PIL import Image, ImageDraw
im = Image.open(path)
draw = ImageDraw.Draw(im)
draw.rectangle(bbox, outline="red", width=4)
```

## Image masking

Faces are masked in all images. 85 images additionally carry frosted-glass masks over logos, characters and other items flagged in a manual IP review. Masks leave the answer region readable.

## Files

- `visualneedle_300en.jsonl` — annotations (300 lines)
- `images/` — masked images (300 lossless `.png` files, ~3.2 GB total). Filenames are the sample `id`.
- `images_bbox/` — same images with the answer bbox drawn (for visualization / sanity check), ~3.2 GB. Filenames are the sample `id`.

Hosted on Tencent COS (older masks for some images, see `image_url` above):
```
https://visualneedle2-1311238981.cos.ap-guangzhou.myqcloud.com/datasets/visualneedle_mask_face_300en/
```

## Changelog

### 2026-11

- Replaced 9 samples that failed quality review with 9 new samples (3 Color, 3 Entity, 2 OCR, 1 Spatial).
- Fixed the question of 8 samples and the answer of 9 samples; moved the bbox of 2 samples to the corrected answer and redrew their bbox images.
- Added `alt_answers` (26 samples) and `neg_answers` (18 samples).
- Updated 85 images with the IP-review masks.
