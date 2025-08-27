# WHO Sample Dataset (Wide Format) - Data Cleaning Prep

This dataset simulates a WHO-style wide-format table containing case counts by country, year, sex, and age group. We will demonstrate how to transform it into tidy format using pandas.

```python
import pandas as pd

data = {
    "country": ["AD", "AE", "AF", "AG", "AL", "AM", "AN", "AO", "AR", "AS"],
    "year": [2000]*10,
    "m014":   [0, 2, 52, 0, 2, 2, 0, 186, 97, None],
    "m1524":  [0, 4, 228, 0, 19, 152, 0, 999, 278, None],
    "m2534":  [1, 4, 183, 0, 21, 130, 1, 1003, 594, None],
    "m3544":  [0, 6, 149, 0, 14, 131, 2, 912, 402, None],
    "m4554":  [0, 5, 129, 0, 24, 63, 0, 482, 419, 1],
    "m5564":  [0, 12, 94, 0, 19, 26, 0, 312, 368, 1],
    "m65":    [0, 10, 80, 1, 16, 21, 0, 194, 330, None],
    "mu":     [None, None, None, None, None, None, None, None, None, None],
    "f014":   [None, 3, 93, 1, 3, 1, 0, 247, 121, None]
}

df_who = pd.DataFrame(data)
df_who.head(10)
```

| country | year | m014 | m1524 | m2534 | m3544 | m4554 | m5564 | m65  | mu   | f014 |
|---------|------|------|--------|--------|--------|--------|--------|------|------|------|
| AD      | 2000 | 0.0  | 0.0    | 1.0    | 0.0    | 0.0    | 0.0    | 0.0  | None | NaN  |
| AE      | 2000 | 2.0  | 4.0    | 4.0    | 6.0    | 5.0    | 12.0   | 10.0 | None | 3.0  |
| AF      | 2000 | 52.0 | 228.0  | 183.0  | 149.0  | 129.0  | 94.0   | 80.0 | None | 93.0 |
| AG      | 2000 | 0.0  | 0.0    | 0.0    | 0.0    | 0.0    | 0.0    | 1.0  | None | 1.0  |
| AL      | 2000 | 2.0  | 19.0   | 21.0   | 14.0   | 24.0   | 19.0   | 16.0 | None | 3.0  |
| AM      | 2000 | 2.0  | 152.0  | 130.0  | 131.0  | 63.0   | 26.0   | 21.0 | None | 1.0  |
| AN      | 2000 | 0.0  | 0.0    | 1.0    | 2.0    | 0.0    | 0.0    | 0.0  | None | 0.0  |
| AO      | 2000 | 186.0| 999.0  | 1003.0 | 912.0  | 482.0  | 312.0  | 194.0| None | 247.0|
| AR      | 2000 | 97.0 | 278.0  | 594.0  | 402.0  | 419.0  | 368.0  | 330.0| None | 121.0|
| AS      | 2000 | NaN  | NaN    | NaN    | NaN    | 1.0    | 1.0    | NaN  | None | NaN  |

```python
df0 = df_who.copy()

# Step 1: Melt wide to long
long = pd.melt(
    df0,
    id_vars=["country", "year"],
    var_name="sex_and_age",
    value_name="cases"
)

# Step 2: Filter only valid columns like m014/f5564
long = long[long["sex_and_age"].str.match(r"^[mf]\d{3,4}$", na=False)].copy()

# Step 3: Extract sex and age ranges
tmp = long["sex_and_age"].str.extract(r"(\D)(\d+)(\d{2})")
tmp.columns = ["sex", "age_lower", "age_upper"]
tmp["age"] = tmp["age_lower"] + "-" + tmp["age_upper"]

# Step 4: Merge and clean
df = pd.concat([long, tmp], axis=1)
df = df.drop(columns=["sex_and_age", "age_lower", "age_upper"])
df = df.dropna(subset=["cases"])
df = df.sort_values(["country", "year", "sex", "age"]).reset_index(drop=True)

df.head(10)
```

| country | year | sex | age   | cases |
|---------|------|-----|--------|--------|
| AD      | 2000 | m   | 0-14  | 0.0    |
| AD      | 2000 | m   | 15-24 | 0.0    |
| AD      | 2000 | m   | 25-34 | 1.0    |
| AD      | 2000 | m   | 35-44 | 0.0    |
| AD      | 2000 | m   | 45-54 | 0.0    |
| AD      | 2000 | m   | 55-64 | 0.0    |
| AE      | 2000 | m   | 0-14  | 2.0    |
| AE      | 2000 | m   | 15-24 | 4.0    |
| AE      | 2000 | m   | 25-34 | 4.0    |
| AE      | 2000 | m   | 35-44 | 6.0    |
