# Shelf Detection — Merchandising Computer-Vision Service + Mobile App

> A computer-vision pipeline that turns field-merchandiser shelf photos into structured
> share-of-shelf, assortment, and competitor analytics — designed and built end-to-end.

## Problem

Measure on-shelf reality at scale, directly from photos:

- **share of shelf** (own brand vs. competitors),
- **assortment coverage** (which SKUs are present),
- **package/format and size mix**,

without manual tagging, and accurately enough to drive business decisions. The hard constraint:
an **honest** number. A pipeline that confidently mislabels competitor packs as own product
inflates the headline metric — so abstaining ("Unknown") is preferable to a confident wrong
answer.

## Architecture

![Shelf detection CV pipeline](../assets/shelf-detection-pipeline.png)

A staged, asynchronous pipeline with self-hosted GPU inference:

```
mobile capture app
   → ingestion API → durable work queue → async worker
        → object detection (YOLO / open-vocabulary)         # localize packs
        → OCR + Vision-Language model                       # read brand/label text
        → visual embeddings (DINOv2 ViT-L/14)               # KNN over a confirmed-product gallery
        → retrieval-augmented matching (vector DB: Qdrant)  # SKU identification
        → rule-based fusion + guardrail layer               # combine signals, abstain when unsure
        → metrics (share of shelf, assortment, coverage)
```

- **Detection:** YOLO and open-vocabulary detection localize product packs; a dedup stage removes
  contained/overlapping/duplicate boxes and shelf-band false positives.
- **OCR / VLM:** each crop is read by a Vision-Language OCR model; brand tokens are matched
  against a normalized brand vocabulary.
- **Embeddings + retrieval:** DINOv2 visual embeddings feed a KNN gallery of confirmed products;
  in parallel, retrieval over a **Qdrant** vector database provides SKU candidates.
- **Fusion + guardrails:** a rule-based layer fuses OCR / retrieval / visual evidence with an
  explicit priority order, and a set of guardrails reject or relabel low-evidence matches. A
  geometry-based *within-shelf* resolver disambiguates same-brand siblings (e.g. weight/format
  variants) using bounding-box layout when the label text is unreadable.
- **GPU inference:** detection, embeddings, OCR, and the VLM run on a self-hosted **NVIDIA H200**.

## My role

I designed and built the **entire pipeline**: detector integration and dedup, the OCR/VLM and
embedding services, the retrieval/matching layer, the fusion and guardrail logic, the within-shelf
geometric resolver, the training/evaluation harness, and the catalog-normalization tooling. I used
LLMs as a **teacher/auditor** for label adjudication and dataset construction — never as
uncontrolled production inference.

## Stack

`PyTorch` · `YOLO / open-vocabulary detection` · `DINOv2` · `Vision-Language OCR` · `Qdrant` ·
`RAG` · `ArcFace metric learning` · `FastAPI` · `PostgreSQL` · `self-hosted S3-compatible object
storage` · `Docker` · `NVIDIA H200 GPU inference`

## Results

- **Detection F1: 0.68 → 0.91 on unseen (out-of-sample) photos.**
- **Catalog normalization: 34 → 20 categories** — collapsing duplicated/ambiguous classes that
  were degrading matching.
- **~300 false positives eliminated** via a targeted regex/normalization fix in the label path.
- **Package classifier ~92% overall accuracy**, evaluated leak-free with grouped-by-image splits
  and cross-store stress tests (one photo can contain many correlated crops, so naive splits leak).
- Solved a long-standing **canister-vs-bottle** confusion by training a dedicated calibrated head
  on visual embeddings, lifting recall on that class from ~14% to the mid-90s while holding high
  precision — promoted to production behind a validated allowlist.

## Engineering highlights

- **Honest-Unknown design:** guardrails explicitly abstain; I quantified that a large share of
  "Unknown" boxes were *intentional competitor rejections*, not coverage gaps — important context
  for interpreting the headline metric correctly.
- **Shadow → active rollout:** every model/guardrail change runs in shadow and is measured on a
  real population before promotion; promotions are gated and reversible in one step.
- **Evaluation honesty:** I learned (and enforced) that curated subsets overstate accuracy —
  a resolver measuring 98% on a curated set dropped to ~82% on the full population, so validation
  is always population-level before any production change.
- **Metric-learning track:** an ArcFace head on frozen visual embeddings closed a large
  packshot→shelf retrieval gap (R@1 ~19% → ~75%); I killed a re-ranking variant after measuring
  that it inverted accuracy on disputed cases — negative results acted on, not shipped.
