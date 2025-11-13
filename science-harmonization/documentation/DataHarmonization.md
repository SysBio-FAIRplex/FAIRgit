# Data Curation

Data curation is critical for transforming heterogeneous data into a harmonized, analysis-ready resource. This process ensures data is FAIR, enabling researchers to leverage the full potential of the data accessible via the FAIRplex platform.

This section describes the outputs from the data harmonization and interoperability pipelines, which include:

-   Harmonized metadata describing datasets
-   Interoperable participant-level data
-   Standardized summary-level results from data analyses.

The structure and content of these outputs are described in more detail in the Data Output section.


## Dataset-level metadata

A FAIRplex dataset is a set of files containing participant-level data of one or more data modalities that meet a specific governance requirement.

Dataset-level metadata is key to finding and cataloging data in FAIRplex and includes:

-   Searchable variables describing assays, biosamples, and disease focus
-   Consent codes describing the data use limitations
-   Relevant experimental documentation and analysis code
-   Data location and provenance
-   Information about data access

## Interoperable individual-level data

Interoperable individual-level data includes clinical data for de-identified individuals that has been made interoperable across datasets, and (as proposed) made compatible with the OMOP open community data standards that can be browsed by tools such as the Verily Data Explorer and FAIRkit.

Individual clinical data is linked to interoperable genetics and omics data. From an analyst-ready perspective, clinical data is a simple data frame linked to collection(s) of common data elements (CDEs), packaged alongside other normalized counts table data frames for each omics modality (proteomics, transcriptomics) within an HDF database, all interoperable. To ease use and tool accessibility, industry-standard formats for genetic data are employed for both variant naming and file formats.

## Summary-level data

Summary-level data derived from the analysis of harmonized or interoperable datasets will be generated in a [schema compatible with HugeAMP](https://hugeamp.org/), allowing for visualization in the FAIRplex portal. HugeAMP is an AMP partnered portal that specializes in modeling, sharing, and visualizing omics and genetic summary-level data from a variety of sources within an established data model. Results prioritized for visualization include summary statistics from differential expression analyses, loadings from principal components analyses, and output from genome-wide association studies (GWAS).

Summary data contains the following data fields:

-   Provenance - the input dataset(s)
-   Status - disease designation or control status
-   Treatment - known interventional treatment group, particularly useful for AMPs with only disease cases or trial-relevant data
-   Tissue - analyte sample was extracted from, generally isolated DNA/RNA, whole blood, mononucleocytes, or a brain region
-   Cell type - cell type annotated via omic clustering methods (i.e. uniform manifold approximation and projection)
-   Beta - from linear or logistic regression
-   Standard error - from linear or logistic regression
-   P value - from linear or logistic regression
-   Test - either the gene or genetic variant name of interest
-   PC1-PC10 - principal component vector loadings
-   N - count of samples in the analysis

A brief summary of the most common data formats per modality is as follows:

| Modality | Raw data format from contributing sources | Intermediate format | Format for data browsers | Analysis ready format | Scale of data |
|:-----------|:-----------|:-----------|:-----------|:-----------|:-----------|
| Clinical data | REDCap and/or tabular | SQL database | OMOP compatible database | Tabular | Gigabytes |
| Genetics | iDat, FASTQ and CRAM occasionally VCF or PLINK2 binary | Joint call VCF or PLINK file | Tabular | PLINK2 binary (allele dosages) | Gigabytes to Terabytes |
| Genomics | iDat, FASTQ and CRAM occasionally VCF or PLINK2 binary | Various | Tabular, normalized and scaled counts per gene | Tabular, normalized and scaled counts per gene | Gigabytes to terabytes |
| Summary level | Tabular | Tabular | Tabular | Tabular | Megabytes to gigabytes |

Unless otherwise specified, all genomic and genetic data use gr38 references. For more details please see the section on Data Output.

------------------------------------------------------------------------

# Data Wrangling

The primary goal of the data wrangling in the context of interoperability and harmonization is to create consistent schemas and values across data from various AMP Projects, facilitating analysis across heterogeneous sets of input data. We are focusing on creating AI-ready data, which, at its core, is about data interoperability; The same column header should represent the same information content and the same numeric space across all datasets. Data that cannot be harmonized from raw omics or genetics will be made interoperable using standard conventions as described below. Dataset-level metadata and summary-level analysis results are derived data that can be standardized to a uniform schema to facilitate research.

## Clinical data

Built into FAIRkit is <a href="https://www.medrxiv.org/content/10.1101/2024.10.17.24315618v1">a resource of over 100K common data elements (CDEs)</a>, including clinical, demographic, experimental, and governance-related data fields. While NLM vetted CDEs are foundational to this resource, novel CDEs that do not overlap with existing National Library of Medicine CDEs will be indexed at <a href="https://cde.nlm.nih.gov/home">NLM’s CDE repository</a> in the future to contribute to public knowledge resources and set a standard for upcoming research. CDEs will be organized to represent topics specifically labeled for easy findability, with these labels referred to as “collections” in the FAIRkit browser. All CDEs that appear in multiple datasets (\> 1) that have been ingested by the FAIRplex team will be annotated as part of this collection, bespoke fields occurring in only one dataset will not be browsable in this iteration of FAIRkit but this may change based on ongoing user feedback.

Each CDE entry contains aliases from published datasets and literature as well as expected values per data field represented by the CDE. CDEs have been modified to be explicit in their naming as opposed to ICD10 codes or similar abbreviations. Clinical, demographic, experimental and governance related CDEs representing data fields from FAIRplex connected studies are audited using an AI-assisted human-in-the-loop protocol to validate content and representativeness. All CDEs will be OMOP compliant whenever possible, following <a href="https://www.ohdsi.org/data-standardization/">NLM CDE conventions</a>.

Clinical data is dataset specific in many cases and cannot be reprocessed in the same way as \*-omic or genetic data. In this case, FAIRkit will flag values outside of the tolerance levels of established CDEs for manual curation and alteration on a case-by-case basis.

## Genetics and omics data

The initial focus of omics data standardization will be on three data modalities (these may be expanded on over time).

-   Transcriptomics (bulk RNA sequencing or pseudobulk stratified by cell type for single cell or single nucleus)
-   Targeted and untargeted proteomics (from mass spectrometry or arrays)
-   Genetics (from whole genome sequencing or imputed array data).

Depending on the availability of either raw data in standard formats (CRAM, iDat, FASTQ) or data processed into gene expression counts or variant calls, data will be either harmonized or made interoperable. The outputs in either case include PLINK2 binary format for genetic data and counts tables per gene or other functional unit for other omics. For each dataset, notebooks containing code for the harmonization or interoperability work will be provided through FAIRkit and FAIRgit.

### Interoperable genetics and omics

Creating interoperable datasets has three goals: (1) variables should have the same names representing the same data field across datasets, (2) they should be on equivalent numeric scales, and (3) they should be able to be merged together and/or subsetted when the need arises.

Genetic data (from arrays and sequencing) is standardized to dosages of alternate alleles from the reference sequence (gr38) and columns are annotated in the $CHR\_$POS\_$REF\_$ALT format as rsIDs and other nomenclature can be non-specific or redundant. These take the form of PLINK2 binaries.

For transcriptomics and proteomics, normalized counts tables are provided with unique sample IDs. These normalized counts are aggregated on the gene level and appended with ENSEMBL gene IDs, as they are unique and specific. These normalized counts are surveyed for skewness and kurtosis in an automated manner and reviewed by analysts. When necessary, to normalize the data log transformations are undertaken. Residualization via linear regression is used to account for dataset-level covariates such as batch, chemistry, or cell composition, etc on a case-by-case basis for statistical adjustment. Normalized and adjusted data is then z-transformed with either case and/or control groups per dataset as a reference point to scale the data into a uniform numeric space. Principal components analysis of all interoperable omics data is used to generate an additional FAIRplex-specific set of covariates, with 10 component loadings to be available for use in analyses across data sources. Counts data and principal components take the form of tabular data easily readable in R or python housed alongside additional participant-level metadata in dataset-specific HDF files discussed later in the Data Modeling vignette. Industry standard packages such as <a href="https://www.nature.com/articles/s41592-019-0619-0">Harmony</a> will be leveraged for this processing.

## Summary level data

Summary-level data is aggregate data summarized across multiple research participants resulting from FAIRplex initiated analyses. These data will be hosted directly by the FAIRplex platform and made available for analysis and visualization through FAIRplex supported tools.

Examples of summary-level data include:

-   Differential expression of harmonized transcriptomics datasets (prioritizing bulk and single cell transcriptomics), which will be carried out using appropriate covariates and standard logistic regression models for all combinations of case/control and treated/untreated status across and within each dataset.
-   Principal component loading per sample, to help visualize the proximity of each dataset to the others within the same numeric projection.
-   GWAS associations summarizing SNP coefficients for various traits in the dataset(s).

Code used for the analysis will be provided to the public in FAIRkit and FAIRgit to deploy and adapt. In addition, other upcoming data wrangling and analysis tooling will be available in Year 2 to facilitate easy meta-analyses across summary-level data provided by the FAIRplex team.

## Additional genetic and omic analysis resources

SysBio FAIRplex stakeholders have prioritized the ability to analyze data across AMP projects for common inflammatory signatures. To address this need, we have identified two areas of community-enabling research resources. The goal is to adopt these with a focus on inflammation and ensure they can scale easily to address multiple disease constellations after receiving feedback from the community.

-   We will generate data-driven networks within known pathways in disease tissues (and cell-types) stratified by case/control or treated/untreated status, highlighting genes in inflammatory pathways using <a href="https://www.nature.com/articles/s41531-022-00288-w">established codebases from the SDH team</a>.

-   We will perform pathway and network enrichment analyses from summary statistics for all differential expression analyses, using foundational genetic pathways from the Molecular Signatures Database and the network communities. This will use an existing codebase from the SDH team that has also been <a href="https://pmc.ncbi.nlm.nih.gov/articles/PMC8422160/">previously published</a>.

## Harmonized data

The initial work will focus on making data interoperable. Raw genetic or omics data can be directly harmonized using processes specific to each modality. Transcriptomic and proteomic data from contributing AMPs will be jointly reprocessed from raw data files using tooling developed by members of the Science and Data Harmonization (SDH) team such as ProtPipe or the CARD <a href="https://pubmed.ncbi.nlm.nih.gov/38168437/">Unified Multi-omics Pipeline</a>. Genetic data will be reprocessed using a similar approcah <a href="https://pubmed.ncbi.nlm.nih.gov/38585876/">such as</a>, <a href="https://pubmed.ncbi.nlm.nih.gov/30279509/">and</a>.

Due to the computationally intensive nature of processing raw omics data the focus for now has been on interoperable data. The use of standard pipeline tooling as described above will prospectively harmonize datasets and generally ensure interoperability and efficiency.

------------------------------------------------------------------------

# Data Output

## Outputs from Data Harmonization Pipelines and Schema

SysBio data pipelines process data from source AMP data into a file hierarchy containing harmonized row-level and summary data for each data modality.

#### 📦 Harmonized Layer (raw standardized data)

| Folder | Contents | Format(s) | Notes |
|--------|----------|-----------|-------|
| `clinical_meta/` | `tabular_data.hdf`, `read_me.txt` | HDF5 | Clinical metadata (CDE-aligned) |
| `bulk_omics/` | `tabular_data.hdf`, `read_me.txt` | HDF5 | Bulk RNA-seq, tissue annotated |
| `cell_omics/` | `tabular_data.hdf`, `read_me.txt` | HDF5 | Single-cell pseudobulk counts |
| `genetics/` | `tabular_data.hdf`, `imputed_data.pgen`, `joint_call.pgen`, `annotation.vcf`, `read_me.txt` | HDF5, PLINK2, VCF | Imputed + joint-called genotypes |

---

### 📊 Summary Layer (precomputed analysis outputs)

| Folder | Contents | Format(s) | Notes |
|--------|----------|-----------|-------|
| `bulk_omics/` | `PCA.hdf`, `networks.hdf`, `differential_expression.hdf`, `read_me.txt` | HDF5 | PCA, network models, DE results |
| `cell_omics/` | same as above | HDF5 | Cell-type level analytics |
| `genetics/` | `PCA.hdf`, `GWAS.hdf`, `read_me.txt` | HDF5 | PCA + GWAS summary stats |
| `browsers/` | `external_links.txt`, `read_me.txt` | text | Links to SysBio Portal, etc. |
| `tools_utils/` | `external_links.txt`, `read_me.txt` | text | Companion tools (Harmony, etc.) |

User friendly naming conventions and file formats have been prioritized to lower activation energy for analyses. All HDF5 format files will contain multiple levels of key designated data frames, a majority of which will be counts tables from various omics technologies. The keys will be clearly described in the readme files and will also be named using a user friendly / self-describing style.

Relevant modality data, formatted as described in the [data standardization section](?tab=t.xt7nyccwmpal#heading=h.d950dhj8scei), will be located in the harmonized directory. Summary level data, segmented by modality, will be housed in a dedicated directory. The following SysBio [Common Data Model section](?tab=t.m9m0xmtyjx82#heading=h.59m6yqko37th) will provide a detailed data model explanation, outlining the integration of HDF files with clinical data and metadata.

## Graphical overview of FAIRkit user flow

### Step 1: Federated authentication for the user
![fed_auth](assets/image_4.png)

### Step 2: Analyze and subset harmonized data to datasets and participants of interest
![viewer](assets/image_5.png)


### Step 3: Build a map of your data to take to Verily Workbench
![fed_auth](assets/image_6.png)


### Step 4: Explore existing summary-level data in the portal
![fed_auth](assets/image_7.png)


Data Output](?tab=t.3ddxfjyv3an) section.

