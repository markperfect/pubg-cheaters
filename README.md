# Analyzing Cheating in PUBG

This project studies the **social contagion of cheating** in [PlayerUnknown's Battlegrounds (PUBG)](https://www.pubg.com/), an online multiplayer battle royale game. The central question is whether players who observe cheating are more likely to start cheating themselves.

## Research question

Do players who observe a cheater become cheaters themselves within a short time window?

We define an **observer–cheater motif** as a player who:

1. Observed cheating in a match (either by watching a cheater kill at least 3 players before being killed, or by being killed by a cheater), and
2. Started cheating within the next **5 days**.

To test whether this pattern reflects causation rather than coincidence, we compare motif counts in the real data against counts from **randomized counterfactual matches**.

## Methodology

### Step 1: Identify observers and new cheaters

For each match, kills are processed in chronological order. A player is treated as having observed cheating if either:

- A cheater kills at least 3 other players before that player is killed in the same match, or
- The player is killed by a cheater.

We then count how many such observers later appear in the cheater registry with a cheating start date within 0–5 days of the observation.

### Step 2: Simulate alternative universes

To approximate a null distribution, kill order within each match is shuffled **10 times**, with two constraints:

- Randomization happens **within** a match, not across matches.
- Cheaters keep their original position in the kill sequence (only non-cheaters are reassigned).

Each randomized dataset is saved as `shuffled_data_1` … `shuffled_data_10`.

### Step 3: Compare actual vs. randomized counts

The number of observer–cheater motifs in the actual data is plotted as a vertical line against a histogram of counts from the 10 randomizations. If observing cheating caused cheating, we would expect **fewer** motifs in randomized worlds than in reality.

**Finding:** More observer–cheater motifs appear in the randomized data than in the actual data, which suggests that observing cheating does **not** make players more likely to cheat.

## Project structure

| File | Description |
|------|-------------|
| `main.py` | Entry point: loads data, runs analysis, and plots results |
| `process_raw_data.py` | Reads and parses `kills.txt` and `cheaters.txt` |
| `cheater_motifs.py` | Identifies observers and counts new cheaters |
| `shuffle.py` | Randomizes kill order within matches and saves pickle files |
| `PUBG Project Overview.ipynb` | Assignment brief, data description, and `%run main.py` demo |

## Requirements

- Python 3.8+
- [matplotlib](https://matplotlib.org/)

Only fundamental Python data structures are used (lists, dictionaries, etc.). Pandas, NetworkX, and similar libraries are intentionally not used.

## Data

Raw data files are **not included** in this repository for privacy reasons. Place them in a sibling directory named `assignment-final-data/`:

```
../assignment-final-data/
├── kills.txt
└── cheaters.txt
```

### `cheaters.txt`

Tab-separated records of players who cheated between **March 1–10, 2019**:

| Column | Description |
|--------|-------------|
| `player_id` | Account ID |
| `cheating_date` | Estimated date cheating started (`YYYY-MM-DD`) |
| `banned_date` | Date the account was banned (`YYYY-MM-DD`) |

### `kills.txt`

Tab-separated kill events from **6,000 randomly selected matches** (March 1–10, 2019):

| Column | Description |
|--------|-------------|
| `match_id` | Match identifier |
| `killer_id` | Account ID of the killer |
| `player_killed_id` | Account ID of the victim |
| `attack_time` | Timestamp of the kill (`YYYY-MM-DD HH:MM:SS.ffffff`) |

## Usage

From the project root, with data files in place:

```bash
python main.py
```

Or run the overview notebook, which executes the same pipeline:

```python
%run main.py
```

Running `main.py` will:

1. Load and parse the raw data
2. Count observer–cheater motifs in the actual data
3. Generate 10 randomized datasets (`shuffled_data_*`)
4. Display a histogram comparing actual vs. randomized motif counts

## License

See repository history for authorship. Data remains subject to the original assignment's privacy restrictions.
