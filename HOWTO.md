"""Exhaustive dump of every results artifact: prints each CSV and meta JSON in full.

Reads from two directories (default results/ and results_why/, overridable via the OUT and
OUT_WHY environment variables). Pure reader: no recomputation, no model loading, no torch.
Run after run_experiments.py finishes, or mid-run to inspect partial output.

Usage
  python scripts/dump_results.py
  OUT=results OUT_WHY=results_why python scripts/dump_results.py
"""
import os
import json
import glob
import pandas as pd

OUT = os.environ.get("OUT", "results")
OUTW = os.environ.get("OUT_WHY", "results_why")

# show everything -- no truncation of rows, columns, or cell width
pd.set_option("display.max_rows", None)
pd.set_option("display.max_columns", None)
pd.set_option("display.width", None)
pd.set_option("display.max_colwidth", None)


def _rule(c="="):
    print(c * 100)


def dump_csv(path):
    _rule()
    print(f"FILE: {path}")
    try:
        df = pd.read_csv(path)
    except Exception as e:
        print(f"  <could not read: {type(e).__name__}: {e}>")
        return
    print(f"shape: {df.shape[0]} rows x {df.shape[1]} cols")
    print(f"columns ({len(df.columns)}): {list(df.columns)}")
    _rule("-")
    if df.empty:
        print("  <empty>")
    else:
        # full, column-by-column, every value -- survives very wide tables
        with pd.option_context("display.max_rows", None, "display.max_columns", None,
                               "display.width", None, "display.max_colwidth", None):
            print(df.to_string(index=True))
    print()


def dump_json(path):
    _rule()
    print(f"FILE: {path}")
    try:
        obj = json.load(open(path))
    except Exception as e:
        print(f"  <could not read: {type(e).__name__}: {e}>")
        return
    print(json.dumps(obj, indent=2, ensure_ascii=False, default=str))
    print()


# ---- enumerate every artifact in both dirs (sorted, deterministic) ----
all_files = []
for d in (OUT, OUTW):
    if os.path.isdir(d):
        all_files += sorted(glob.glob(os.path.join(d, "*")))
    else:
        print(f"[warn] dir not found: {d}")

_rule()
print("ARTIFACTS FOUND:")
for f in all_files:
    print("  ", f, f"({os.path.getsize(f)} bytes)")
print()

for f in all_files:
    if f.endswith(".csv"):
        dump_csv(f)
    elif f.endswith(".json"):
        dump_json(f)
    else:
        _rule()
        print(f"FILE: {f}  <skipped: not csv/json>")
        print()

# ---- compact cross-file summary of the headline result ----
t2 = os.path.join(OUT, "table2_dissociation.csv")
if os.path.exists(t2):
    _rule()
    print("HEADLINE SUMMARY (table2_dissociation):")
    df = pd.read_csv(t2)
    keep = [c for c in ["model", "nEN", "R2_full", "R2_EN", "R2_rand",
                        "gap_paired_point", "gap_paired_lo", "gap_paired_hi", "gap_paired_p",
                        "verdict", "verdict_NEW", "verdict_NEW_seedmin",
                        "R2_posctrl_corrH", "R2_posctrl_pca", "EN_act_var", "entropy_var"]
            if c in df.columns]
    with pd.option_context("display.max_rows", None, "display.max_columns", None,
                           "display.width", None, "display.max_colwidth", None):
        print(df[keep].to_string(index=False))
    if "verdict_NEW" in df.columns:
        print("\nverdict_NEW counts:", df["verdict_NEW"].value_counts(dropna=False).to_dict())
