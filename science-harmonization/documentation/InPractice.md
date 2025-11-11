## A Guide to SysBio-FAIRplex Through User Journeys
**Operationalizing the Common Data Model**

Science and Data Harmonization Core

October 2025

------------------------------------------------------------------------
The SysBio FAIRplex platform enables cross-disease systems biology research by making AMP Program data Findable, Accessible, Interoperable, and Reusable (FAIR). This document demonstrates how researchers operationalize the SysBio Common Data Model (CDM) through two distinct user journeys:

1.  **The Data Consumer**: Uses FAIRkit to discover federated data, build cohorts, and analyze interoperable datasets in Verily Workbench.

2.  **The Data Producer**: Makes siloed genetic, clinical, and multi-omic data interoperable for cross-study meta-analyses

Both journeys leverage Common Data Elements (CDEs), standardized pipelines, and open-source code shared via FAIRgit.

------------------------------------------------------------------------

## **The SysBio FAIRplex Ecosystem Overview**

### **Core Components**

**FAIRkit**: Search and discovery tool for finding datasets across AMP programs - Dataset Explorer: Browse CDEs and dataset-level metadata - Federated Browser: Query harmonized data with federated authentication - Cohort Builder: Create “shopping lists” of samples across multiple AMPs

**SysBio CDM**: Common Data Model based on OMOP, extended for genetics and omics - Clinical data: OMOP-compliant relational tables - Genetics: PLINK2 format with standardized variant naming - Omics: HDF5 format with normalized, scaled counts per gene

**FAIRgit**: Central GitHub organization for reproducible code and documentation

**Verily Workbench**: Cloud-based analysis environment with access to expansive centralized/federated data

**SysBio FAIRplex Portal**: Public-facing visualization of summary-level data and connections to other sources

### **Data Architecture**
---

## **DATA STRUCTURE**

---

<details>
<summary><strong>📁 harmonized_$(date)/</strong></summary>

```
clinical_meta/
└── tabular_data.hdf            # OMOP-compliant CDEs

bulk_omics/
└── tabular_data.hdf            # Normalized counts, z-scored

cell_omics/
└── tabular_data.hdf            # Pseudobulked, cell-type annotated

genetics/
├── tabular_data.hdf            # Variant annotations + covariates
├── imputed_data.pgen           # PLINK format
└── joint_call.pgen             # PLINK format
```
</details>

<details>
<summary><strong>📁 summary_$(date)/</strong></summary>

```
bulk_omics/
├── differential_expression.hdf
├── networks.hdf
└── PCA.hdf

cell_omics/
└── differential_expression.hdf

genetics/
└── GWAS.hdf
```
</details>

------------------------------------------------------------------------

## **User Journey 1: The Data Consumer**

### **Finding, Exploring, and Analyzing Federated Data**

**Persona**: Dr. Sarah Chen, a mid-career computational biologist investigating inflammatory signatures across neurodegenerative diseases (Alzheimer’s, Parkinson’s) and autoimmune conditions (RA, SLE).

### **Phase 1: Discovery with FAIRkit**

#### *Step 1: Discovery of FAIRkit through the SysBio FAIRplex Portal*

Sarah explores the SysBio FAIRplex Portal for documentation and tools

#### *Step 2: Federated Authentication*

<p align="center">
<code>FAIRkit User</code> ➜ <code>FAIRplex Service</code> ➜ <code>Passport Broker (RAS)</code>
<br><br>
⬇️
<br><br>
<code>AMP-AD Visa</code>  <code>AMP-PD Visa</code>
</p>

Her authenticated session now provides access to datasets across AMP AD, AMP PD, AMP RA/SLE, and AMP X based on her approved Data Access Requests (DARs).

#### *Step 3: Dataset Search and Exploration*

Sarah navigates to the **FAIRkit Dataset Explorer** and searches for: - **Disease focus**: Alzheimer’s disease, Parkinson’s disease, Rheumatoid Arthritis - **Data modalities**: Genomics (bulk RNA-seq), Genetics (WGS) - **Biospecimen**: Brain tissue, Blood

The search returns 7 datasets similar to below:

| Dataset | AMP | Disease Focus | Data Types | Biospecimen | Access |
|:-----------|:-----------|:-----------|:-----------|:-----------|:-----------|
| A | AMP AD | Alzheimer’s | Genomics, Genetics, Clinical | Brain | Controlled |
| B | AMP AD | Alzheimer’s | Genomics, Genetics | Brain | Controlled |
| C | AMP PD | Parkinson’s | Genomics, Genetics | Brain, Blood | Controlled |
| D | AMP RA/SLE | RA | Genomics, Genetics | Blood | Controlled |

Each dataset displays: - **Consent codes** via GA4GH DUO terms - **Sample counts** stratified by case/control - **Common Data Elements** available (age, sex, disease duration, APOE genotype, etc.) - **Data location** (AMP portal + FAIRplex bucket)

#### *Step 4: Exploring Harmonized Metadata*

Sarah selects Datsets **A, B** and **C** study views in the **Federated Browser**, which queries the harmonized metadata:

**Clinical Variables (mapped to OMOP CDM)**: - Person: age_at_baseline, sex, race, APOE_genotype - Condition: diagnosis, age_at_diagnosis, cognitive_status - Measurement: mmse_score, moca_score, braak_stage - Specimen: tissue_type, PMI, RIN

**Omics Data**: - 15,234 genes with normalized, z-transformed bulk RNA-seq counts - Cell-type pseudobulked data: neurons, astrocytes, microglia, oligodendrocytes

**Genetics**: - 8.2M variants (imputed + sequenced) in PLINK2 format - Variant naming: $CHR\_$POS\_$REF\_$ALT (e.g., 1_12345_A_G)

### **Phase 2: Cohort Building**

Sarah wants to focus on cases with moderate-to-severe AD pathology and controls, matched for age and sex.

**Cohort Criteria**: - **Cases**: braak_stage \>= 4, diagnosis == "Alzheimer's" - **Controls**: braak_stage \<= 2, cognitive_status == "NCI" - **Age**: 70-85 years - **Tissue**: Dorsolateral prefrontal cortex (DLPFC)

She applies filters in FAIRkit and generates summary statistics:

| Dataset | Cases (n) | Controls (n) | Mean Age | \% Female | Tissue |
|:--------|:----------|:-------------|:---------|:----------|:-------|
| A  | 287       | 143          | 79.0     | 58%       | DLPFC  |
| B    | 156       | 89           | 81.9     | 52%       | DLPFC  |

**Cohort Export**: Sarah downloads a **Sample Manifest** (JSON) that includes: - participant_id, dataset, and pointers to data files in FAIRplex buckets and source AMP portals

### **Phase 3: Analysis in Verily Workbench**

Sarah uploads the Sample Manifest to Verily Workbench, which provisions a secure cloud environment with: - **Data access**: Transient, temporary access to FAIRplex-harmonized data for her cohort - **Compute resources**: 16-core VM with 128GB RAM - **Analysis tools**: R, Python, Bioconductor packages

#### *Analysis Workflow*

**Step 1: Load Harmonized Data**
```
import pandas as pd\
import h5py

# Load clinical metadata
clinical = pd.read_hdf('harmonized_20250915/clinical_meta/tabular_data.hdf', key='a_b_clinical')

# Load bulk RNA-seq (normalized, z-transformed)
h5py.File('harmonized_20250915/bulk_omics/tabular_data.hdf', 'r') as f:
a_counts = f$$'a_dlpfc_bulk'$$$$:$$
b_counts = f$$'b_dlpfc_bulk'$$$$:$$
gene_ids = f$$'gene_ensembl_ids'$$$$:$$

# Load genetics (PLINK2)
from pandas_plink import read_plink1_bin
genotypes = read_plink1_bin('harmonized_20250915/genetics/imputed_data.pgen')
```
**Key Features of Harmonized Data**: - **Same column names** across datasets (e.g., age_at_baseline, not age1, Age, age_diagnosis) - **Same numeric scale**: Z-transformed expression values (mean=0, SD=1) enable direct comparison - **Linked by person_id**: Clinical, omics, and genetics data join seamlessly via participant_id

**Step 2: Differential Expression Analysis**

Sarah runs a meta-analysis across Datasets A and B:
```
from scipy.stats import ttest_ind
import statsmodels.api as sm

# Example: Gene-level t-tests (cases vs. controls)
results = []

for gene_idx in range(len(gene_ids)):
a_case = a_counts$$clinical['diagnosis'$$=='AD', gene_idx]

a_ctrl = a_counts$$clinical['diagnosis'$$=='NCI', gene_idx]

t_stat, p_val= ttest_ind(a_case, a_ctrl)  
results.append({'gene': gene_ids\[gene_idx\], 't': t_stat, 'p': p_val})
results_df = pd.DataFrame(results)
```

Because data is **z-transformed**, effect sizes are comparable across studies. Sarah identifies 342 genes with FDR \< 0.05, enriched for: - Inflammatory pathways (IL6, TNF signaling) - Microglial activation (TREM2, CD33)

**Step 3: Cross-Disease Comparison**

Sarah repeats the analysis with AMP PD and AMP RA/SLE data, leveraging the **same CDEs** and **same pipeline outputs** to compare inflammatory signatures:

| Pathway       | AD (Dataset A) | PD (Dataset C) | RA (Dataset D) | Overlap  |
|:--------------|:------------|:--------------|:------------|:---------|
| IL6 signaling | ↑ 2.3-fold  | ↑ 1.8-fold    | ↑ 3.1-fold  | 18 genes |
| TNF signaling | ↑ 1.9-fold  | ↑ 1.4-fold    | ↑ 2.7-fold  | 12 genes |

### **Phase 4: Sharing Results via FAIRgit**

Sarah publishes her analysis code to FAIRgit:

<details>
<summary><strong>📁 SysBio-FAIRPlex/inflammatory-signatures-AD-PD-RA\/</strong></summary>

```
├── README.md
├── cohort_manifest.json # if able to be shared, pending discussion
├── differential_expression.R
├── pathway_enrichment.py
└── figures
└── volcano_plot.png
```
</details>

Her summary statistics are also uploaded to the **SysBio Portal** for public visualization.

------------------------------------------------------------------------

## **User Journey 2: The Data Producer**

### **Making Siloed Data Interoperable**

**Persona**: Dr. James Rodriguez, a bioinformatician at the newly formed AMP X, tasked with integrating newly generated single-cell RNA-seq and genetics data from an inflammatory bowel disease (IBD) cohort into FAIRplex.

### **Phase 1: Understanding Requirements**

James reviews the **SysBio CDM documentation** and identifies three data types to harmonize:

1.  **Clinical data**: Demographics, disease severity, treatment history

2.  **Genetics**: Whole genome sequencing (WGS)

3.  **Single-cell RNA-seq**: Intestinal biopsy samples

**Key Challenges**: - Clinical variables use non-standard names (e.g., PatientAge vs. age_at_baseline) - WGS variants are annotated with dbSNP rsIDs (not standardized CHR_POS_REF_ALT) - scRNA-seq data is in Seurat object format (H5ad, which is technically not HDF5)

### **Phase 2: Clinical Data Mapping to CDEs**

James accesses the **FAIRkit CDE Browser**, which contains CDEs from NLM and FAIRplex datasets.

#### *Step 1: CDE Matching*

He cross-references his data dictionary to FAIRkit’s **AI-assisted CDE mapping tool**:

| Original Variable | CDE Match | OMOP Table | Notes |
|:-----------------|:-----------------|:-----------------|:-----------------|
| PatientAge | age_at_baseline | Person | Exact match |
| Gender | sex | Person | Values: M→Male, F→Female |
| Disease_Duration_Years | disease_duration_years | Condition | New CDE (not in NLM) |
| HarveyBradshawIndex | hbi_score | Measurement | Maps to IBD severity |
| BiologicTreatment | treatment_biologic | Drug_Exposure | Values: Yes/No |

This CDE will be mirrored to the **NLM CDE Repository** for future reuse and interoperability by matching column names and information content and scale.

#### *Step 2: OMOP Transformation*

James runs a Python script to transform his clinical data into OMOP CDM format:
```
# Map to PERSON table

person_df = pd.DataFrame({
'person_id': ibd_data$$'SubjectID'$$,'birth_datetime': 2025 - ibd_data$$'PatientAge'$$, # Derive from age
'gender_concept_id': ibd_data$$'Gender'$$.map({'M': 8507, 'F': 8532}),
'race_concept_id': ibd_data$$'Race'$$.map(race_mapping)
})

# Map to CONDITION_OCCURRENCE*
condition_df = pd.DataFrame({
'person_id': ibd_data$$'SubjectID'$$,
'condition_concept_id': 201606, # SNOMED: Crohn's disease
'condition_start_date': ibd_data$$'DiagnosisDate'$$,
'condition_source_value': 'IBD'
})

# Save as HDF5
person_df.to_hdf('clinical_meta/tabular_data.hdf', key='X_clinical')
```
### **Phase 3: Genetics Data Harmonization**

James’ WGS data is in VCF format with non-standardized variant IDs. He uses the **FAIRplex genetics pipeline** to create interoperable data.

#### *Step 1: Variant Standardization*
```
# Convert to PLINK2 with standardized naming
plink2 --vcf ibd_wgs.vcf.gz
--set-all-var-ids '\@:#:$r:$a'
--new-id-max-allele-len 100
--make-pgen
--out X_standardized

# Output: X_standardized.pgen (PLINK2 binary)
# Variants now named: chr1_12345_A_G (not rs123456)
```
#### *Step 2: Quality Control and Imputation*

Following the **CARD Unified Pipeline** as shown on FAIRkit’s genetics examples, James: 1. Filters variants (MAF \> 0.01, HWE p \> 1e-6, genotype call rate \> 95%) 2. Imputes missing genotypes using TOPMed reference panel 3. Generates principal components (PCs) to capture population structure
```
# Save genetics metadata with covariates
genetics_meta = pd.DataFrame({
'person_id': sample_ids,
'PC1': pcs$$:, 0$$,
'PC2': pcs$$:, 1$$,
*\# ... PC3-PC10*
'array_or_wgs': 'WGS',
'imputation_panel': 'TOPMed'
})
genetics_meta.to_hdf('genetics/tabular_data.hdf', key='X_ibd_genetics')
```
**Output Files**: - imputed_data.pgen: 10.3M variants in PLINK2 format - tabular_data.hdf: Metadata + PCs for covariate adjustment

### **Phase 4: Single-Cell RNA-seq Harmonization**

James’ scRNA-seq data is in Seurat format (R object). He converts it to FAIRplex-compatible HDF5 with pseudobulked counts.

#### *Step 1: Cell Type Annotation*

Using **Harmony** (a tool recommended in FAIRplex documentation), James integrates his dataset with existing intestinal scRNA-seq references:

library(Seurat)
library(harmony)
```
# Load data*
seurat_obj <- **readRDS**('ibd_scrna.rds')

# Harmonize with reference (e.g., AMP X intestinal atlas)
seurat_obj <- **RunHarmony**(seurat_obj, group.by.vars = 'dataset')

# Annotate cell types
seurat_obj <- FindClusters(seurat_obj, resolution = 0.5)
cell_types <- ('T_cell', 'B_cell', 'Epithelial', 'Macrophage', ...)
seurat_obj$cell_type <- cell_types[seurat_obj$seurat_clusters]
```
#### *Step 2: Pseudobulking and Normalization*

James aggregates counts by cell type and normalizes:

**library**(DESeq2)
```
# Pseudobulk by cell type
pseudobulk <- AggregateExpression**(seurat_obj,
group.by = ('person_id', 'cell_type'),
return.seurat = FALSE)

# Normalize and z-transform
dds <- DESeqDataSetFromMatrix**(countData = pseudobulk,
colData = metadata,
design = ~ diagnosis)
dds <- estimateSizeFactors**(dds)
normalized_counts <- counts(dds, normalized=TRUE)

# Z-transform (mean=0, SD=1) for interoperability
z_transformed <-scale(log2(normalized_counts + 1))
```
**Why Z-transformation?** - Ensures **same numeric scale** across datasets (critical for meta-analysis) - Accounts for technical variation (batch effects, sequencing depth) - Enables direct comparison with existing FAIRplex datasets

#### *Step 3: Export to HDF5*
```
import h5py

withh5py.File('cell_omics/tabular_data.hdf', 'w') as f:
f.create_dataset('X_ibd_intestine_tcells', data=z_transformed$$'T_cell'$$)

f.create_dataset('aim_ibd_intestine_macrophages', data=z_transformed$$'Macrophage'$$)
f.create_dataset('gene_ensembl_ids', data=gene_ids)
```
### **Phase 5: Summary Statistics Generation**

James runs standard analyses to generate summary-level data for the **SysBio Portal**:

#### *Differential Expression*
```
from scipy.stats import ttest_ind

# Compare IBD cases vs. controls (T cells)
results = []
for gene **in** gene_ids:
    case_expr = z_transformed$$'T_cell'$$$$metadata['diagnosis'$$=='IBD', gene]
    ctrl_expr = z_transformed$$'T_cell'$$$$metadata['diagnosis'$$=='Control', gene]

        
    beta, se, p_val = ttest_ind(case_expr, ctrl_expr)  
        results.append({  
        'gene': gene,  
        'cell_type': 'T_cell',  
        'tissue': 'intestine',  
        'beta': beta,  
        'se': se,  
        'p_value': p_val,  
        'n_cases': len(case_expr),  
        'n_controls': len(ctrl_expr),  
        'dataset': 'X_IBD'  
    })

# Save to HDF5*
results_df = pd.DataFrame(results)
results_df.to_hdf('summary_20250915cell_omicsdifferential_expression.hdf',
key='X_ibd_tcell_de')
```
#### *GWAS*
```
# Run PLINK2 association test
plink2 --pgen ibd_standardized.pgen 
--pheno ibd_phenotype.txt 
--covar genetics_covariates.txt 
--glm 
--out ibd_gwas

# Convert to HDF5
python convert_gwas_to_hdf5.py ibd_gwas.assoc.glm
```
**Summary Data Schema** (compatible with SysBio Portal):

| Field      | Description                                          |
|:-----------|:-----------------------------------------------------|
| test       | Gene/variant name (ENSEMBL or CHR_POS_REF_ALT)       |
| beta       | Effect size (log2 fold-change or allele coefficient) |
| se         | Standard error                                       |
| p_value    | P-value from statistical test                        |
| n          | Sample size                                          |
| tissue     | Tissue/cell type (e.g., ‘intestine’, ‘T_cell’)       |
| status     | Case/control or treatment group                      |
| provenance | Source dataset (e.g., ‘X_IBD’)                       |

### **Phase 6: Data Return to AMP X**

Once data is harmonized, James coordinates the **data return** via email:

To: AMP X Data Governance Team\
Subject: Harmonized IBD Data Return

Dear Team,

Please find the harmonized AIM_IBD dataset, now interoperable with\
FAIRplex standards:

1. Clinical data: OMOP CDM format (2,340 participants)
2. Genetics: PLINK2 format with standardized variant naming (10.3M variants)
3. Single-cell RNA-seq: HDF5 format with pseudobulked, z-transformed counts

Data location: gs://fairplex-amp-X/harmonized_ibd_20250915/

Summary statistics are now live in the SysBio Portal for public exploration.

Code and documentation: <https://github.com/SysBio-FAIRPlex/X-ibd-harmonization>

Best regards,
James Rodriguez

### **Phase 7: Code Sharing via FAIRgit**

James publishes his harmonization pipeline to FAIRgit:

<details>
<summary><strong>📁 SysBio-FAIRPlex/X-ibd-harmonization/</strong></summary>

```
├── README.md
├── clinical_cde_mapping.json
├── genetics_pipeline.sh
├── scrna_pseudobulk.R
├── summary_statistics.py
└── notebooks/
├── 01_clinical_omop_transform.ipynb
├── 02_genetics_qc_imputation.ipynb
└── 03_scrna_harmony_integration.ipynb
```
</details>


**Key Pipeline Outputs**:

| Data Type | Input Format  | Output Format     | Tools Used                  |
|:----------|:--------------|:------------------|:----------------------------|
| Clinical  | CSV (custom)  | HDF5 (OMOP)       | Python, FAIRkit CDE mapping |
| Genetics  | VCF           | PLINK2 (.pgen)    | PLINK2, TOPMed imputation   |
| scRNA-seq | Seurat (.rds) | HDF5 (pseudobulk) | Seurat, Harmony, DESeq2     |
| Summary   | N/A           | HDF5              | Python (scipy, statsmodels) |

## **Key Principles Enabling Interoperability**

### **1. Common Data Elements (CDEs)**

**Problem**: Different AMPs use different variable names for the same concept - AMP AD: age1, AMP PD: Age, AMP RA/SLE: age_at_baseline

**Solution**: FAIRkit’s CDEs provide canonical names and definitions - **Mapping**: age1 → age_at_baseline (CDE: NLM-12345) - **Validation**: Expected values, units, and ranges - **Discoverability**: Organized into “collections” (demographics, clinical, experimental)

### **2. Standardized Data Formats**

**Clinical**: OMOP CDM relational tables (Person, Condition, Measurement, etc.)

**Genetics**: - PLINK2 binary (.pgen) for genotypes - Variant naming: CHR_POS_REF_ALT (e.g., 1_12345_A_G) - Reference genome: GRCh38

**Omics**: - HDF5 format for scalability - Normalized, z-transformed counts (mean=0, SD=1) - Gene IDs: ENSEMBL (unique, specific)

### **3. Uniform Processing Pipelines**

**Example: Bulk RNA-seq Interoperability Pipeline**

Raw FASTQ → Alignment (STAR) → Quantification (RSEM)\
→ Normalization (DESeq2) → Batch Correction (ComBat-Seq)\
→ Z-transformation → HDF5 export

**Why Uniform Pipelines Matter**: - **Comparable effect sizes**: Z-scores enable meta-analysis - **Reduced technical variation**: Same algorithms across datasets - **Reproducibility**: Code shared via FAIRgit

**Tools**: CARD Unified Pipeline, ProtPipe, Harmony

### **4. Linked Data via person_id**

All data types connect via person_id (de-identified):

Person (OMOP) ───┬─── Condition_Occurrence\
├─── Drug_Exposure\
├─── Measurement\
├─── Specimen ──── omics_table ──── HDF5 (omics data)\
└─── genetics_table ──── PLINK2 (genotypes)

This enables queries like: > “Find all participants with APOE4 genotype, Braak stage 5-6, and differential expression of TREM2 in microglia”

------------------------------------------------------------------------

## **Summary: Operationalizing FAIR Principles**

| FAIR Principle | How FAIRplex Operationalizes It |
|:-----------------------------------|:-----------------------------------|
| **Findable** | FAIRkit Dataset Explorer with searchable CDEs, consent codes, and metadata |
| **Accessible** | Federated authentication via Passport Broker; data in AMP portals + FAIRplex buckets |
| **Interoperable** | OMOP CDM, PLINK2, HDF5, ENSEMBL IDs; z-transformed omics data; uniform pipelines |
| **Reusable** | Open-source code in FAIRgit; summary data in SysBio Portal; documentation |

### **Impact on Research**

**Before FAIRplex**: - Researcher spends 6 months manually harmonizing clinical variables across 3 AMPs - Cannot compare RNA-seq data due to different normalization methods - Meta-analysis requires re-contacting data generators

**With FAIRplex**: - Researcher builds cohort in FAIRkit in 2 hours - Analyzes harmonized data in Verily Workbench immediately - Meta-analysis uses standardized summary statistics in SysBio Portal - Pipeline code is reusable for new datasets via FAIRgit

------------------------------------------------------------------------

## **Conclusion**

The SysBio FAIRplex platform transforms fragmented AMP data into a **federated, interoperable ecosystem** by:

1.  **Standardizing metadata** via Common Data Elements (CDEs)

2.  **Harmonizing data formats** (OMOP, PLINK2, HDF5)

3.  **Normalizing numeric scales** (z-transformation for omics)

4.  **Providing uniform pipelines** (CARD, ProtPipe, Harmony)

5.  **Enabling federated access** (FAIRkit, Verily Workbench)

6.  **Sharing code and results** (FAIRgit, SysBio Portal)

Both user journeys—the **Data Consumer** and **Data Producer**—demonstrate how researchers operationalize the SysBio CDM to accelerate cross-disease systems biology research while adhering to FAIR principles.

------------------------------------------------------------------------

## **Resources**

-   **SysBio FAIRplex Portal**: <https://sysbio-portal-347771964579.us-central1.run.app>
  
-   **FAIRkit**: <https://fairkit-498260019893.us-central1.run.app>
------------------------------------------------------------------------

*Document Version: 1.0*\
*Last Updated: October 15, 2025*\
*Maintained by: Science and Data Harmonization Core*
