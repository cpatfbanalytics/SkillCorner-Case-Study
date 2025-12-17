# SkillCorner NFL Draft Evaluation: Wide Receiver Athletic Profiling

A data-driven analysis of how tracking-derived athletic traits predict NFL draft success for college wide receivers. This project demonstrates that player tracking data outperforms traditional production metrics in predicting draft outcomes and identifies four distinct receiver archetypes with dramatically different draft rates.

## 📊 Project Overview

**Research Question:** Which on-field athletic traits predict NFL draft success, and can tracking data add value beyond traditional box score statistics?

**Key Findings:**
- Vertical speed shows the largest difference between drafted and undrafted players (+0.61 SD)
- COD/Agility is the strongest single predictor of draft success (r = 0.30)
- SkillCorner tracking model (AUC = 0.831) outperforms PFF production model (AUC = 0.798)
- Four distinct WR archetypes identified with 5x draft rate difference (20.3% vs 3.9%)
- Separation ability shows near-zero correlation with draft capital despite NFL importance

## 🔬 Methodology

### Data Sources
- **SkillCorner Tracking Data**: 682 college WRs from 2022 season (30+ raw metrics)
- **PFF Production Data**: Routes run, receiving stats, target rates (98.7% coverage)
- **NFL Draft Data**: Manual compilation of 2023-2025 drafts (101 WRs, 75 matched)

### Feature Engineering
Created 5 composite tracking scores from raw metrics:

1. **Vertical Speed Score**: Max speed (99th %), max speed 30+ yards, flying 20-yard dash
2. **Burst Score**: Max speed 0-10 yards, high acceleration events per 100 plays
3. **Separation Score**: Average separation (99th %), separation vs man coverage, COD-generated separation
4. **COD/Agility Score**: Change of direction speed (180°, 90°), route entry/exit speeds
5. **YAC Potential Score**: Separation change post-throw, change of direction on routes

All metrics z-scored for standardization.

### Analysis Pipeline
1. **Data Cleaning**: Name normalization, missing value handling, PFF data type conversion
2. **Feature Engineering**: Composite score creation, route depth bucketing
3. **Draft Matching**: Conservative exact-match algorithm (74% coverage, zero false positives)
4. **Correlation Analysis**: Pearson/Spearman correlations with draft outcomes
5. **Predictive Modeling**: Logistic regression (L2 regularization, C=1.0)
6. **Clustering**: K-means (k=4) with silhouette validation
7. **Archetype Assignment**: Rule-based naming using centroid trait profiles

## 📈 Key Results

### Trait Differences (Drafted vs Undrafted)
| Trait | Mean Difference | Significance |
|-------|-----------------|--------------|
| Vertical Speed | +0.61 SD | p < 0.001 |
| Burst | +0.32 SD | p < 0.001 |
| COD/Agility | +0.30 SD | p < 0.001 |
| YAC Potential | +0.08 SD | n.s. |
| Separation | +0.04 SD | n.s. |

### Model Performance
| Model | Features | AUC | n |
|-------|----------|-----|---|
| SkillCorner Only | 6 tracking metrics | 0.831 | 669 |
| PFF Only | 4 production metrics | 0.798 | 591 |
| Combined | 10 metrics | 0.856 | 586 |

### WR Archetypes
| Archetype | Size | Draft Rate | Avg Pick | Key Traits |
|-----------|------|------------|----------|------------|
| Dynamic Playmaker | 202 (30%) | 20.3% | 123 | Elite across all traits |
| Vertical Threat | 187 (28%) | 12.8% | 139 | Top-end speed, negative YAC |
| Route Technician | 152 (23%) | 3.9% | 120 | High separation, average speed |
| Developmental | 128 (19%) | 3.1% | 176 | Below average all traits |

## 🚀 Usage

### Setup
```bash
git clone https://github.com/yourusername/skillcorner-wr-draft-analysis.git
cd skillcorner-wr-draft-analysis
pip install -r requirements.txt
```

### Running the Analysis
```python
# Open Jupyter notebook
jupyter notebook notebooks/wr_draft_analysis.ipynb

# Run cells 1-9 sequentially:
# Cell 1: Setup & imports
# Cell 2: Data loading
# Cell 3: Feature engineering
# Cell 4: Draft data + PFF merge
# Cell 5: Correlation & regression
# Cell 6: K-means clustering
# Cell 7: Summary visualization
# Cell 8: Undervalued UDFA analysis
# Cell 9: Draft capital efficiency
```

### Key Functions
```python
# Name normalization for draft matching
def normalize_name_strict(name):
    """Conservative name cleaning for exact matching"""
    clean = str(name).lower().replace('.', '').replace('-', ' ').replace("'", '').strip()
    for suffix in [' jr', ' sr', ' iii', ' ii', ' iv']:
        if clean.endswith(suffix):
            clean = clean[:-len(suffix)].strip()
    return clean

# PFF data cleaning
def convert_to_numeric(df, cols):
    """Handle percentage strings and commas in PFF data"""
    for col in cols:
        if col in df.columns:
            df[col] = df[col].astype(str).str.replace('%', '').str.replace(',', '').str.strip()
            df[col] = pd.to_numeric(df[col], errors='coerce')
    return df
```

## ⚠️ Limitations

1. **Time Gap**: 1-3 year lag between measurement (2022) and draft (2023-2025). Athletes develop over time, reducing predictive accuracy for 2025 draftees.
2. **Sample Size**: Only 75 drafted players limits statistical power for complex models.
3. **Missing Validation**: No NFL performance data to validate which archetypes actually succeed.
4. **Selection Bias**: SkillCorner dataset may not include all FCS/low-division players who get drafted.
5. **Competition Quality**: Tracking metrics don't adjust for strength of schedule or conference.

## 🔮 Future Work

- [ ] Add NFL combine metrics (40-time, 3-cone, vertical) for validation
- [ ] Incorporate multi-year tracking data to measure development trajectories
- [ ] Link to NFL performance metrics (PFF grades, EPA, target rate)
- [ ] Build position-specific models (slot vs boundary receivers)
- [ ] Adjust tracking metrics for strength of schedule
- [ ] Expand to other positions (RB, TE, DB)

## 📚 Dependencies
```
pandas >= 2.0.0
numpy >= 1.24.0
matplotlib >= 3.7.0
seaborn >= 0.12.0
scikit-learn >= 1.3.0
scipy >= 1.10.0
```

## 🎯 Practical Applications

**For NFL Teams:**
1. **Pre-Draft Filtering**: Flag small-school WRs with elite tracking traits for tape review
2. **Draft Day Validation**: Use tracking to validate tape grades and break ties
3. **Archetype Matching**: Filter draft boards by archetype to match scheme needs
4. **UDFA Targeting**: Identify Dynamic Playmakers with limited college usage

**For Agents/Players:**
1. Understand which traits teams value most (speed > separation)
2. Focus training on improvable traits (burst, agility)
3. Use archetype framework for draft positioning strategy

**For Media/Analytics:**
1. Quantify "athletic upside" narratives with data
2. Identify potential reaches/steals based on trait-draft capital mismatches
3. Track archetype success rates over time

## 📝 Citation

If you use this analysis or methodology, please cite:
```
SkillCorner WR Draft Analysis (2024)
Tracking-Based Athletic Profiling for NFL Draft Evaluation
https://github.com/yourusername/skillcorner-wr-draft-analysis
```

## 📧 Contact

For questions or collaboration opportunities:
- Email: your.email@example.com
- LinkedIn: linkedin.com/in/yourprofile
- Twitter: @yourhandle

## 📄 License

This project is licensed under the MIT License - see LICENSE file for details.

## 🙏 Acknowledgments

- SkillCorner for providing college tracking data
- Pro Football Focus (PFF) for production statistics
- Anthropic Claude for analytical assistance

---

**Note**: This analysis is for educational/portfolio purposes. SkillCorner data used with permission for case study evaluation.
