# SQL shortcuts for Fabric notebooks and SSMS

Type a short code and press **Space**, and it turns into SQL. Works in **Microsoft Fabric notebooks in Chrome** (Spark SQL and PySpark) and in **SQL Server Management Studio** (T-SQL), using [Espanso](https://espanso.org), a free text expander.

```
st100 + Space   →   SELECT *
                    FROM |
                    LIMIT 100
```

The shortcuts are switched on **only** in Fabric tabs in Chrome and in SSMS. In email, Word, Teams or any other website, nothing expands.

For VS Code, use the [SQL Shortcuts extension](https://github.com/chaya-abramovitz/vscode-sql-shortcuts) instead. It has more features (Tab between spots, a visual editor, settings).

## Set up

1. **Install Espanso.** In a terminal:
   ```
   winget install --id Espanso.Espanso --source winget
   ```
   Then open **Espanso** from the Start menu and click through its welcome screen (Start → Continue → Continue → Got it).
2. **Download this repo.** Click **Code → Download ZIP** above and unzip it, or `git clone` it.
3. **Install the shortcuts.** You need [Node.js](https://nodejs.org). In the repo folder, run:
   ```
   node setup.js
   ```
   This writes the shortcut sets and the app rules into `%APPDATA%\espanso`. Espanso picks them up by itself.
4. **Try it:** open a Fabric notebook in Chrome, type `ssf` and press Space.

**To update:** download the repo again and run `node setup.js` again.

## Using it

- Type a shortcut, then **Space**. The cursor lands where you type next.
- Shortcuts that are also common words or table aliases start with a semicolon, so they never fire by accident: `;cte` `;rn` `;j` `;go` `;is` `;ct` `;dt` `;pk` `;fk` `;use` `;at` `;ex`.
- **Search all shortcuts:** press **Alt+Space** while in Fabric or SSMS.
- **Pause:** right-click the Espanso icon in the system tray.

## Fabric (Spark SQL and PySpark)

| Shortcut | Gives you |
|---|---|
| `ssf` / `sf` | `SELECT * FROM` / `SELECT columns FROM` |
| `st10` `st100` `st1000` | `SELECT * FROM … LIMIT n` |
| `sd` `scf` `sw` `sso` | DISTINCT / COUNT(*) / WHERE / ORDER BY |
| `scg` / `dupes` | Count per group / find duplicates |
| `w2` `wi` `wlk` `wb` `wn` `wnn` | WHERE, IN, LIKE, BETWEEN, IS NULL, IS NOT NULL |
| `jn` `ij` `lj` `rj` `fj` `cj` | JOIN / INNER / LEFT / RIGHT / FULL / CROSS JOIN |
| `lsj` / `laj` | LEFT SEMI JOIN (has a match) / LEFT ANTI JOIN (no match) |
| `un` `ua` `ob` `obd` `gb` `hv` `lm` | UNION, UNION ALL, ORDER BY, GROUP BY, HAVING, LIMIT 100 |
| `ii` `is` `io` | INSERT … VALUES / INSERT … SELECT / INSERT OVERWRITE |
| `up` `dfw` `mrg` | UPDATE / DELETE … WHERE / Delta MERGE |
| `;ct` `ctas` `cv` `ctv` `;dt` | CREATE TABLE (Delta) / … AS SELECT / view / temp view / DROP TABLE |
| `dsc` `dh` `shw` `opt` `vac` | DESCRIBE / DESCRIBE HISTORY / SHOW TABLES / OPTIMIZE / VACUUM |
| `;cte` `;rn` `rk` `lg` `ld` `rt` `cw` | CTE, ROW_NUMBER, RANK, LAG, LEAD, running total, CASE |
| `coa` `cst` `gd` `gts` `dta` `dtd` `dfmt` `tdt` `cws` `clst` | Functions: coalesce, cast, dates, concat_ws, collect_list |
| `sqlc` / `pysc` | `%%sql` / `%%pyspark` cell |
| `sps` / `dfs` / `dsp` | `spark.sql("""…""")` / query into `df` and display / `display()` |
| `rtb` / `dfr` | `spark.read.table("…")` / `df = spark.read.table("…")` |
| `rcsv` `rpq` `rjs` | Read CSV / Parquet / JSON from `Files/` |
| `wov` / `wap` | Write to a Delta table: overwrite / append |
| `impf` | `from pyspark.sql import functions as F` and `Window` |
| `flt` `gby` `wcol` `sel` `srt` `jdf` | `.filter` `.groupBy().agg` `.withColumn` `.select` `.orderBy desc` `.join` |
| `shwt` `pdf` | `.show(20, truncate=False)` / `.toPandas()` |
| `nfs` / `nrun` | `notebookutils.fs.ls` / `notebookutils.notebook.run` |

The full list is in [spark.json](spark.json).

## SSMS (T-SQL)

The same 120+ shortcuts as the [VS Code extension](https://github.com/chaya-abramovitz/vscode-sql-shortcuts#shortcuts): `ssf`, `st100`, `ij`, `lj`, `up`, `;cte`, `cp`, `tc` and so on. If you use the extension, `setup.js` copies **your own** list from it, including your edits. Otherwise it uses [tsql/tsql.json](tsql/tsql.json).

## Change the shortcuts

- **Fabric:** edit `spark.json`, then run `node setup.js`. Each entry is `"prefix"` (what you type), `"description"` and `"body"`. In the body, `$1` marks where the cursor goes.
- **SSMS:** change them in the VS Code extension's editor (or `tsql/tsql.json`), then run `node setup.js`.

## How it works

`setup.js` writes four files into `%APPDATA%\espanso`:

| File | What it does |
|---|---|
| `match\_spark.yml`, `match\_tsql.yml` | The two shortcut sets. Files starting with `_` aren't switched on anywhere by themselves. |
| `config\fabric.yml` | Switches on the Spark set when Chrome's window title ends in "Fabric - Google Chrome" or "Power BI - Google Chrome" (Fabric uses both). |
| `config\ssms.yml` | Switches on the T-SQL set in `Ssms.exe`. |

To check which shortcuts are active in a window: `espanso match list -t --exec "<program path>" --title "<window title>"`.

**Limits:** Espanso has one cursor position per shortcut (no Tab between spots). The Fabric rule goes by the window title, so a shortcut typed in any text box on a Fabric page (not just code cells) will expand too.
