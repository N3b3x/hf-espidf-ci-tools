# Documentation Structure

This directory contains the Jekyll-based documentation for the HardFOC ESP32 CI Tools.

## Directory Structure

```
docs/
├── _config.yml          # Jekyll configuration
├── _data/               # Data files for includes
│   ├── features.yml     # Feature definitions
│   └── navigation.yml   # Navigation structure
├── _includes/           # Reusable components
│   ├── alert.html       # Alert/notification component
│   ├── badge.html       # Badge component
│   ├── card.html        # Card component
│   ├── feature_list.html # Feature list component
│   ├── nav_list.html    # Navigation list component
│   └── workflow_status.html # Workflow status component
├── _layouts/            # Custom layouts
│   ├── home.html        # Home page layout
│   ├── page.html        # Standard page layout
│   └── workflow.html    # Workflow-specific layout
├── _pages/              # Additional pages
│   └── features.md      # Features showcase page
├── index.md             # Main documentation page
├── build-workflow.md    # Build workflow documentation
├── security-workflow.md # Security workflow documentation
└── example-workflows.md # Example workflows documentation
```

## Usage

To build and serve the documentation locally:

```bash
cd docs
jekyll serve --config _config.yml
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

The `_config.yml` file contains all Jekyll configuration including:
- Site metadata
- Just the Docs theme settings
- Navigation structure
- Plugin configuration
- File exclusions

## Data Files

Data files in `_data/` can be used to populate includes and maintain consistency across the documentation.