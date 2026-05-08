# VisualNeedle 300 (EN)

A visual "needle-in-haystack" benchmark for VLMs: each task asks about a tiny detail in a relatively large image. The model must locate and reason over a small region (median ~0.1% of image area) to answer correctly.

## Stats

- **Size**: 300 samples
- **Language**: English questions, answers may be in English or Chinese (when answer is text in the image)
- **Image resolution**: 599×648 to 7952×8223 (median 1440×1669)
- **Bbox size**: 0.012% – 4.44% of image area (median **0.10%**)

### Categories

| Count | Category | Description |
|----:|---|---|
| 73 | Color Recognition | identify the color of a small object |
| 71 | OCR Recognition | read text from a small region |
| 64 | Entity Recognition | identify a small / partial object |
| 58 | Spatial Relationship | reason about position / direction |
| 34 | Occluded Object Recognition | identify a partially occluded object |

## Schema

Each line in `visualneedle_300en.jsonl` is a JSON object:

| Field | Type | Description |
|---|---|---|
| `id` | string | unique sample id |
| `category` | string | one of the 5 categories above |
| `question` | string | English question |
| `answer` | string | ground-truth answer |
| `image_url` | string | full image URL on COS |
| `image_bbox_url` | string | same image with answer bbox drawn (red) |
| `bbox` | `[x1, y1, x2, y2]` | answer region, **xyxy absolute pixel coords** |

## Example

```json
{
  "id": "TPROMPT9ee217718764",
  "category": "OCR Recognition",
  "question": "In the picture, what is the second character to the right of '8元'?",
  "answer": "辣",
  "image_path": "images/TPROMPT9ee217718764.jpg",
  "image_url": "https://visualneedle2-1311238981.cos.ap-guangzhou.myqcloud.com/datasets/visualneedle_300en/images/TPROMPT9ee217718764.jpg",
  "image_bbox_path": "images_bbox/TPROMPT9ee217718764.jpg",
  "image_bbox_url": "https://visualneedle2-1311238981.cos.ap-guangzhou.myqcloud.com/datasets/visualneedle_300en/images_bbox/TPROMPT9ee217718764.jpg",
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

## Files

- `visualneedle_300en.jsonl` — annotations (300 lines)
- `images/` — original images (300 files, mixed `.jpg` / `.png`, ~742 MB total). Filenames are the sample `id`.
- `images_bbox/` — same images with the answer bbox drawn (for visualization / sanity check). Filenames are the sample `id`.

Hosted on Tencent COS:
```
https://visualneedle2-1311238981.cos.ap-guangzhou.myqcloud.com/datasets/visualneedle_300en/
```
