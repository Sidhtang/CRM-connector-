# CRM-connector-
# CRM Integration Framework

A powerful, extensible Python framework for synchronizing and managing data between different CRM systems with workflow automation, metric tracking, and standardized error handling.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Python](https://img.shields.io/badge/python-3.7%2B-blue)

## Overview

This framework provides a comprehensive solution for CRM data integration, allowing seamless synchronization between different CRM platforms (currently supporting Odoo and Zoho CRM). It's designed with extensibility, error handling, and performance metrics in mind.

## Features

- **Multi-CRM Support**: Connect to and synchronize data between different CRM systems
  - Currently supports Odoo and Zoho CRM
  - Extensible architecture to add more CRM connectors
- **Smart Schema Mapping**: Automated field mapping between different CRM systems
- **Workflow Automation**: Pre-built workflows for common integration scenarios
  - Data synchronization
  - Data querying
  - Record updates
  - Task scheduling
  - Reporting
- **Metrics Collection**: Built-in performance and operational metrics
- **Secure Credential Management**: Simple secret management for API credentials
- **Comprehensive Logging**: Detailed logging for troubleshooting and audits
- **Error Handling**: Robust error handling and reporting

## Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/crm-integration.git
cd crm-integration

# Create a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

## Requirements

- Python 3.7+
- Redis (for metrics collection)
- Dependencies listed in requirements.txt:
  - pandas
  - numpy
  - requests
  - redis
  - xmlrpc.client (for Odoo integration)

## Quick Start

1. Configure your CRM credentials in `secrets.json`:

```json
{
  "odoo": {
    "url": "http://yourodoo.instance.com",
    "database": "odoo_db",
    "username": "admin",
    "password": "your_password"
  },
  "zoho": {
    "client_id": "your_client_id",
    "client_secret": "your_client_secret",
    "refresh_token": "your_refresh_token",
    "domain": "com"
  }
}
```

2. Create schema mapping rules in the `mapping_rules` directory:

```json
// mapping_rules/odoo_to_zoho.json
{
  "First_Name": {
    "type": "transform",
    "transformation": {
      "type": "split",
      "field": "name",
      "separator": " ",
      "index": 0
    }
  },
  "Last_Name": {
    "type": "transform",
    "transformation": {
      "type": "split",
      "field": "name",
      "separator": " ",
      "index": 1
    }
  },
  "Email": {
    "type": "direct",
    "source_field": "email"
  }
}
```

3. Run a simple data sync:

```python
from crm_integration import WorkflowManager

workflow_manager = WorkflowManager()
result = workflow_manager.process_workflow("Sync data from Odoo to Zoho")

if result.success:
    print("Sync completed successfully!")
    print(f"Records processed: {result.data.get('records_processed')}")
    print(f"Records synced: {result.data.get('records_synced')}")
else:
    print(f"Sync failed: {result.messages[0]}")
```

## Architecture

The framework is built with a modular architecture:

### Core Components

- **BaseCRMConnector**: Abstract base class for all CRM connectors
  - **OdooConnector**: Connector for Odoo ERP
  - **ZohoCRMConnector**: Connector for Zoho CRM
- **SchemaMapper**: Handles field mapping between different CRMs
- **WorkflowManager**: Orchestrates different integration workflows
- **MetricsCollector**: Collects and stores performance metrics
- **SimpleSecretManager**: Manages API credentials securely

### Data Structures

- **UserInput**: Stores parsed user input and categorization
- **IntentContext**: Stores parsed user intent and relevant entities
- **WorkflowResult**: Stores the result of workflow execution

## Workflows

The framework supports several pre-built workflows:

1. **Data Sync**: Synchronize data between two CRM systems
2. **Data Query**: Execute queries against a CRM system
3. **Data Update**: Update records in a CRM system
4. **Scheduling**: Schedule recurring tasks
5. **Reporting**: Generate reports from CRM data

## Extending the Framework

### Adding a New CRM Connector

1. Create a new class that inherits from `BaseCRMConnector`
2. Implement the required methods:
   - `authenticate()`
   - `fetch_data()`
   - `push_data()`

Example:

```python
class MyCRMConnector(BaseCRMConnector):
    def authenticate(self) -> bool:
        # Implement authentication logic
        pass
        
    def fetch_data(self, query: Dict[str, Any]) -> List[Dict[str, Any]]:
        # Implement data fetching logic
        pass
        
    def push_data(self, data: List[Dict[str, Any]]) -> bool:
        # Implement data pushing logic
        pass
```

### Creating Custom Workflows

Extend the `WorkflowManager` class to add custom workflows:

```python
class MyWorkflowManager(WorkflowManager):
    def _handle_custom_workflow(self, input_data: UserInput) -> Dict[str, Any]:
        # Implement custom workflow logic
        pass
        
    def process_workflow(self, user_input: str) -> WorkflowResult:
        # Override to add custom workflow handling
        input_data = self.input_handler.categorize_input(user_input)
        
        if input_data.category == "custom_category":
            result = self._handle_custom_workflow(input_data)
            # Process result and return WorkflowResult
        else:
            # Call parent method for standard workflows
            return super().process_workflow(user_input)
```

## Error Handling

The framework provides comprehensive error handling:

- All operations are wrapped in try-except blocks
- Errors are logged with detailed information
- `WorkflowResult` includes detailed error information
- Metrics are collected for failed operations

## Logging

The framework uses Python's built-in logging module:

- Logs are written to both console and a file (`crm_integration.log`)
- Different log levels are used for different types of events
- Detailed error information is included in error logs

## Performance Metrics

The framework collects various performance metrics:

- Execution time for operations
- Resource usage
- API call counts
- Success/failure rates

Metrics are stored in Redis and can be analyzed using the `MetricsCollector` class.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgements

- [Odoo](https://www.odoo.com/)
- [Zoho CRM](https://www.zoho.com/crm/)
