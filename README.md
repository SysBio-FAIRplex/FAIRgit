# FAIRgit - The SysBio-FAIRPlex GitHub Organization Structure and Standards

This is a standard setting document for all github repos coming on to the SysBio-FAIRplex ecosystem.
All technical documentation and publicly shareable artifacts will be published here and <a href="https://docs.github.com/en/repositories/archiving-a-github-repository/referencing-and-citing-content">minted with DOIs</a> at milestones of substantial revision.
Everything from code for analyses to data inventories and even lab documentation should be pushlished to this github organization as part of our efforts to maximize transparency and reproducibility.
Let's go **open science**!

**Please read below before your first commit (3 minute read only)!**

## Overview

FAIRgit serves as the central GitHub organization for the SysBio FAIRplex Project, designed to promote collaborative, transparent, and reproducible research following FAIR (Findable, Accessible, Interoperable, Reusable) principles. This document outlines the organizational structure, development workflows, and coding standards that govern all repositories within the FAIRgit ecosystem.

## Organization Structure

### Team-Based Repository Organization

The FAIRgit organization is structured around eight primary areas, seven corresponding to the specialized teams within the SysBio-FAIRplex Project and one for general content:

#### 1. Prime Platforms (`prime-platforms/`)
- **Purpose**: Core infrastructure and foundational platforms
- **Repository naming**: `prime-*` or `platform-*`
- **Access**: Team leads + designated developers

#### 2. Science, Harmonization and Search (`science-harmonization/`)
- **Purpose**: Data harmonization tools, search algorithms, and scientific methodology
- **Repository naming**: `sci-*`, `harmonize-*`, or `search-*`
- **Access**: Science team + collaborating researchers

#### 3. Program Management and Operations (`program-ops/`)
- **Purpose**: Project management tools, operational scripts, and administrative resources
- **Repository naming**: `ops-*`, `mgmt-*`, or `admin-*`
- **Access**: Program managers + operations team

#### 4. Portal and Knowledge Transfer (`portal-kt/`)
- **Purpose**: User interfaces, web portals, documentation sites, and knowledge dissemination tools
- **Repository naming**: `portal-*`, `web-*`, or `docs-*`
- **Access**: Portal development team + UX designers

#### 5. Governance and Standards (`governance/`)
- **Purpose**: Policy documents, standards definitions, compliance tools
- **Repository naming**: `governance-*`, `standards-*`, or `policy-*`
- **Access**: Governance committee + standards working groups

#### 6. Research Training and Outreach (`training-outreach/`)
- **Purpose**: Educational materials, training modules, workshop content
- **Repository naming**: `training-*`, `education-*`, or `workshop-*`
- **Access**: Training coordinators + educational content developers

#### 7. External Collaborators (`external-collab/`)
- **Purpose**: Forked repositories and collaborative spaces for external partners
- **Repository naming**: `collab-*`, `external-*`, or `partner-*`
- **Access**: Approved external collaborators + project liaisons

#### 8. General SysBio-FAIRplex (`general/`)
- **Purpose**: General documentation used across all SysBio-FAIRplex teams 
- **Repository naming**: `general-*`
- **Access**: SysBio-FAIRplex teams

## Management and Governance

### Organization Administration
- **Primary Administrators**: Representatives from the Program Management and Operations team
- **Secondary Administrators**: Designated leads from each subteam
- **Review Committee**: Cross-team representatives for major organizational decisions

### Access Control
- **Team Leads**: Full access to their team's repositories
- **Developers**: Write access to assigned repositories
- **Collaborators**: Read access with specific write permissions as needed
- **External Partners**: Limited access to designated collaboration repositories

## Development Workflow

### Repository Lifecycle

#### 1. Development Phase (Private Repositories)
- All new development begins in **private staging repositories**
- Repository naming: `staging-[team-prefix]-[project-name]`
- Access limited to development team and designated reviewers
- Regular commits with descriptive messages following conventional commit standards

#### 2. Code Review Process
- **Mandatory peer review**: Minimum 2 reviewers from the relevant team
- **Cross-team review**: Required for repositories affecting multiple teams
- **Standards compliance check**: Automated testing for coding standards adherence
- **Documentation review**: Ensuring README and documentation completeness

#### 3. Production Release (Public Repositories)
- Upon successful review, code is merged to **public production repositories**
- Repository naming: `[team-prefix]-[project-name]`
- Comprehensive documentation and clear licensing
- Tagged releases with semantic versioning

### Branch Protection
- **Main/Master branch**: Protected, requires pull request and review
- **Development branches**: Feature-specific branches for active development
- **Release branches**: Stabilization branches for preparing releases

## README Standards and Template

### Required README Sections

All repositories must include a standardized README.md file with the following sections:

#### Standard README Template Structure:
```
# Repository Title

## Description
Brief description of the project and its purpose within the SysBio FAIRplex Project.

## Installation
Step-by-step installation instructions including dependencies.

## Usage
Clear examples of how to use the code/tools with sample commands or code snippets.

## Data Sources and Attribution
- List all data sources used
- Provide proper citations and attribution
- Include data licensing information

## Contributing
Guidelines for contributing to the project, including coding standards and pull request process.

## Reproducibility
- Instructions for reproducing analyses or results
- Environment specifications (requirements.txt, environment.yml, etc.)
- Version information for critical dependencies

## License
Clearly state the project license and any restrictions.

## Contact
Contact information for maintainers and relevant team / affiliation.

## Acknowledgments
Recognition of contributors, funding sources, and collaborating institutions.
```

### Example README Implementation

Here's how the template should be implemented following the Science, Harmonization and Search team naming conventions:

---

## ⬇️ **EXAMPLE README STARTS HERE** ⬇️

**File: README.md**

# sci-DataProcessor

## Description
A Python toolkit for harmonizing heterogeneous biological datasets as part of the SysBio FAIRplex Project's Science, Harmonization and Search initiative.

## Installation
```bash
pip install -r requirements.txt
python setup.py install
```

## Usage
```python
from sciHarm import DataProcessor
processor = DataProcessor(config="config.yaml")
harmonized_data = processor.harmonize(input_data)
```

## Data Sources and Attribution
- **Primary Dataset**: Smith et al. (2024). "Genomic Data Collection." *Nature Biotechnology*. DOI: 10.1038/example
- **Reference Standards**: FAIR Data Principles Consortium Standards v2.1
- **License**: CC-BY-4.0

## Contributing
Please email maintainer.

## Reproducibility
- Python 3.9+
- See `requirements.txt` for exact package versions
- Run `python reproduce_analysis.py` to replicate published results

## License
MIT License - see [LICENSE](LICENSE) file for details.

## Contact
- **Maintainer**: Dr. Jane Researcher (jane.researcher@institution.edu)
- **Team**: Science, Harmonization and Search Team

## Acknowledgments
This work was supported by the NIH Common Fund. Special thanks to the SysBio FAIRplex Project teams!

## ⬆️ **EXAMPLE README ENDS HERE** ⬆️

---

## Coding Standards and Style Guides

### Python Development
- **Style Guide**: [PEP 8 - Style Guide for Python Code](https://peps.python.org/pep-0008/)
- **Documentation**: [PEP 257 - Docstring Conventions](https://peps.python.org/pep-0257/)
- **Type Hints**: [PEP 484 - Type Hints](https://peps.python.org/pep-0484/)

### Additional Language Standards
- **JavaScript**: [Google JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html)
- **R**: [Google's R Style Guide](https://google.github.io/styleguide/Rguide.html)
- **General**: [Google Style Guides](https://google.github.io/styleguide/)

### Repository Structure Standards

#### Recommended Python Repository Structure

```
project-name/
├── README.md
├── LICENSE
├── requirements.txt
├── setup.py
├── .gitignore
├── .github/
│   ├── workflows/
│   └── ISSUE_TEMPLATE/
├── docs/
│   ├── source/
│   └── build/
├── src/
│   └── project_name/
│       ├── __init__.py
│       ├── core/
│       ├── utils/
│       └── tests/
├── examples/
├── data/
│   ├── raw/
│   ├── processed/
│   └── external/
└── scripts/
```

#### Additional Structure Resources
- [Python Project Structure Guide](https://docs.python-guide.org/writing/structure/)
- [Cookiecutter Data Science Template](https://drivendata.github.io/cookiecutter-data-science/)
- [Scientific Python Development Guide](https://learn.scientific-python.org/development/)

## Supporting FAIR Principles

### How FAIRgit Advances FAIR Data Principles

**Findable**
- Standardized repository naming conventions enable easy discovery
- Comprehensive metadata in README files and repository descriptions
- Consistent tagging and release management
- Cross-referencing between related repositories

**Accessible**
- Open-source licensing for public repositories
- Clear documentation and installation instructions
- Multiple access levels accommodating various collaboration needs
- Persistent URLs and DOI integration for stable referencing

**Interoperable**
- Standardized data formats and API specifications
- Common development frameworks and coding standards
- Cross-team collaboration spaces and shared utilities
- Documentation of data schemas and exchange formats

**Reusable**
- Comprehensive documentation ensuring reproducibility
- Clear licensing and attribution guidelines
- Modular code architecture promoting component reuse
- Version control and release management for stable dependencies

### Integration with SysBio FAIRplex Project Goals

The FAIRgit organization structure directly supports the SysBio FAIRplex Project's mission by:

1. **Facilitating Cross-Team Collaboration**: Organized team spaces with clear interfaces for inter-team projects
2. **Ensuring Quality and Reproducibility**: Standardized review processes and documentation requirements
3. **Promoting Open Science**: Public repositories with comprehensive documentation and clear licensing
4. **Supporting Training and Outreach**: Dedicated spaces for educational materials and external collaboration
5. **Maintaining Standards Compliance**: Governance repositories ensuring adherence to FAIR principles and scientific best practices

## License

MIT License - [https://github.com/SysBio-FAIRPlex/FAIRgit/blob/main/LICENSE.md](https://github.com/SysBio-FAIRPlex/FAIRgit/blob/main/LICENSE.md)

## Contact

- **Maintainer**: Mike A. Nalls, PhD, DataTecnica - mike@datatecnica.com 
- **Team**: Science, Harmonization and Search Team

## Acknowledgments

This work was supported by the NIH Common Fund. Special thanks to the SysBio FAIRplex Project teams!

---

**Document Version**: 0.99 - pending DOI at V1.0. 
**Last Updated**: August 22, 2025  
**Maintained by**: FAIRgit Program Management and Operations Team
