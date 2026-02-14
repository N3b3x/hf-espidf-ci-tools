# Documentation Structure

This directory contains the Jekyll-based documentation for the HardFOC ESP32 CI Tools.

## Directory Structure

```
docs/
├── index.md             # Main documentation page
├── build-workflow.md    # Build workflow documentation
├── security-workflow.md # Security workflow documentation
└── example-workflows.md # Example workflows documentation

_config/                 # All configuration and Jekyll files
├── _config.yml         # Jekyll configuration
├── _data/              # Data files for includes
│   ├── features.yml    # Feature definitions
│   └── navigation.yml  # Navigation structure
├── _includes/          # Reusable components
│   ├── alert.html      # Alert/notification component
│   ├── badge.html      # Badge component
│   ├── card.html       # Card component
│   ├── feature_list.html # Feature list component
│   ├── nav_list.html   # Navigation list component
│   └── workflow_status.html # Workflow status component
├── _layouts/           # Custom layouts
│   ├── home.html       # Home page layout
│   ├── page.html       # Standard page layout
│   └── workflow.html   # Workflow-specific layout
├── _pages/             # Additional pages
│   └── features.md     # Features showcase page
├── _sass/              # Custom SASS styles
├── .markdownlint.json  # Markdown linting config
├── .yamllint           # YAML linting config
└── lychee.toml         # Link checking config
```

## Usage

To build and serve the documentation locally:

```bash
cd docs
jekyll serve --config ../_config/_config.yml
```

The documentation will be available at `http://localhost:4000`.

## Custom Components

### Alerts

```liquid
{% include alert.html type="info" title="Note" content="Important information" %}
```

### Badges

```liquid
{% include badge.html text="New" type="success" %}
```

### Cards

```liquid
{% include card.html title="Card Title" content="Card content" %}
```

### Feature Lists

```liquid
{% include feature_list.html features=site.data.features %}
```

### Workflow Status

```liquid
{% include workflow_status.html status="success" message="Build completed" %}
```

## Configuration

All configuration files are located in the `_config/` directory:

- `_config.yml` - Jekyll configuration (site metadata, theme settings, navigation, plugins)
- `.markdownlint.json` - Markdown linting rules
- `.yamllint` - YAML linting rules
- `lychee.toml` - Link checking configuration

## Data Files

Data files in `_config/_data/` can be used to populate includes and maintain consistency across the documentation.
