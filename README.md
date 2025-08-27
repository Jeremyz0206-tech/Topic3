# Topic3
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
