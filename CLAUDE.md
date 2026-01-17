# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Python package (`ftl_collections`) that provides Ansible Collections for AWS and POSIX systems. The project packages module utilities that are commonly used across multiple Ansible modules for cloud automation and system administration.

## Build and Package Commands

```bash
# Build the package
python -m build

# Install in development mode
pip install -e .

# Install the package
pip install .
```

## Architecture

### Collection Structure
The project follows Ansible Collection standards with two main collections:

1. **amazon.aws** (`src/ansible_collections/amazon/aws/`) - AWS-specific utilities
2. **ansible.posix** (`src/ansible_collections/ansible/posix/`) - POSIX system utilities

### Key Components

#### AWS Collection (`amazon/aws/plugins/module_utils/`)
- `modules.py` - Core `AnsibleAWSModule` class that wraps standard Ansible modules with AWS-specific functionality
- `core.py` - Legacy imports and compatibility layer 
- `botocore.py` - Boto3/Botocore integration utilities
- `retries.py` - AWS API retry logic with backoff strategies
- Service-specific utilities: `ec2.py`, `s3.py`, `iam.py`, `rds.py`, `route53.py`, etc.
- `common.py` - Collection metadata and version information

#### POSIX Collection (`ansible/posix/plugins/module_utils/`)
- `mount.py` - File system mounting utilities
- `firewalld.py` - Firewall management utilities  
- `version.py` - Version management utilities

### Module Development Pattern
AWS modules should inherit from `AnsibleAWSModule` rather than the standard `AnsibleModule`. This provides:
- Automatic AWS credential handling
- Boto3 client/resource creation with retry decorators
- Standardized error handling for AWS API calls
- Built-in support for AWS retry strategies

Example usage:
```python
from ansible_collections.amazon.aws.plugins.module_utils.modules import AnsibleAWSModule

module = AnsibleAWSModule(argument_spec=spec)
client = module.client('ec2', retry_decorator=AWSRetry.jittered_backoff(retries=10))
```

## Package Configuration

The project uses `pyproject.toml` with setuptools for packaging:
- Package name: `ftl_collections` 
- Version: `0.1.7`
- Source directory: `src/`
- Includes all `ansible_collections*` packages