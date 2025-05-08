# IAM Setup Script

A comprehensive Linux Identity and Access Management automation tool for user and group creation with enhanced security and email notifications.

## Overview

The IAM Setup Script (`iam_challenge`) automates the creation of users and groups in Linux systems with specific settings and security measures. It processes user data from a CSV file, creates appropriate user accounts and groups, sets secure temporary passwords, configures password policies, and optionally sends email notifications using the `mail` command with Google SMTP relay support.

## Features

- **Automated User Creation**: Processes user data from CSV files
- **Group Management**: Creates groups if they don't exist
- **Password Policy Enforcement**: Sets temporary passwords with complexity requirements
- **Security Controls**: Forces password change on first login
- **Home Directory Setup**: Creates and secures user home directories
- **Email Notifications**: Sends customizable notifications to users and administrators
- **Postfix Mail Support**: Configured for local mail delivery using Postfix
- **Extensive Logging**: Maintains detailed logs of all operations
- **Flexible Configuration**: Supports command-line options and configuration files

## Prerequisites

- Linux environment with root/sudo access
- `mail` command utility installed
- Google SMTP relay configuration for email delivery

## Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/iam-setup.git
   cd iam-setup
   ```

2. Make the script executable:
   ```bash
   chmod +x iam_challenge
   ```

3. Create a configuration file:
   ```bash
   cp config.env.example .iam_config.env
   ```

4. Edit the configuration file with your settings:
   ```bash
   nano .iam_config.env
   ```

## Postfix Mail Setup

1. Ensure Postfix is properly installed and configured:
   ```bash
   sudo apt-get install postfix mailutils # For Debian/Ubuntu
   # OR
   sudo yum install postfix mailx # For RHEL/CentOS
   ```

2. Configure Postfix as a local mail transfer agent:
   ```bash
   sudo postconf -e "inet_interfaces = loopback-only"
   sudo postconf -e "myorigin = \$mydomain"
   sudo systemctl restart postfix
   ```

3. Update your `.iam_config.env` file:
   ```bash
   EMAIL_FROM="iam-system@yourdomain.com"
   EMAIL_FROM_NAME="IAM System"
   ADMIN_EMAIL="admin@yourdomain.com"
   SMTP_SERVER="localhost"
   SMTP_PORT="25"
   # SMTP_USER and SMTP_PASSWORD not needed for local Postfix
   ```

## Configuration

### Command Line Options

| Option | Description |
|--------|-------------|
| `-f, --file FILE` | Specify input CSV file (default: users.csv) |
| `-l, --log FILE` | Specify log file (default: iam_setup.log) |
| `-p, --password PWD` | Set temporary password (default: AmalitechGTP@Accra123) |
| `-e, --email` | Enable email notifications |
| `-c, --config FILE` | Specify config file (default: .iam_config.env) |
| `-h, --help` | Display help message |

### Configuration File

Create `.iam_config.env` with the following parameters:

```bash
# Email Settings
EMAIL_FROM="iam-system@exampl.com"
EMAIL_FROM_NAME="IAM System"
ADMIN_EMAIL="admin@exampl.com"
SMTP_SERVER="smtp.gmail.com"
SMTP_PORT="587"
SMTP_USER="your-email@gmail.com"
SMTP_PASSWORD="your-app-password"
```

### Input CSV Format

Create a CSV file with the following format:

```csv
username,fullname,group,email
jdoe,John Doe,developers,john.doe@example.com
asmith,Alice Smith,admins,alice.smith@example.com
```

## Usage Examples

### Basic Usage

```bash
sudo ./iam_challenge
```

### Specify Custom CSV File

```bash
sudo ./iam_challenge -f employees.csv
```

### Enable Email Notifications

```bash
sudo ./iam_challenge -e
```

### Custom Password and Log File

```bash
sudo ./iam_challenge -p "SecureTemp#123" -l setup_may2025.log
```

### Complete Example

```bash
sudo ./iam_challenge -f new_hires.csv -l onboarding.log -p "Welcome2025!" -e -c mail_config.env
```

## Password Requirements

The temporary password must meet the following requirements:
- Minimum 8 characters
- At least one lowercase letter
- At least one uppercase letter
- At least one digit
- At least one special character (!@#$%^&*()_+{}|:<>?)

## Email Notifications

When enabled, the system sends:

1. **User notification** containing:
   - Username
   - Group assignment
   - Temporary password
   - Password change requirement

2. **Admin notification** containing:
   - Username
   - Full name
   - Group assignment
   - Email address

## Email Delivery Methods

   **mail** - Default system mail command (configured for Google SMTP)

## Logging

The script generates a detailed log file with timestamps for all operations, including:
- Script start and completion
- Configuration loading
- User and group creation
- Password setting
- Email notification status
- Errors and warnings

Example log output:
```
# IAM Setup Log - Started on 2025-05-08
[2025-05-08 10:15:23] Script started
[2025-05-08 10:15:23] Loaded configuration from .iam_config.env
[2025-05-08 10:15:24] Processing user: jdoe (John Doe) for group: developers
[2025-05-08 10:15:24] Created group: developers
[2025-05-08 10:15:25] Created user: jdoe with home directory
[2025-05-08 10:15:25] Set temporary password for jdoe
[2025-05-08 10:15:25] Password change required at next login for jdoe
[2025-05-08 10:15:25] Set permissions 700 on /home/jdoe
[2025-05-08 10:15:26] Used mail command to send email to john.doe@example.com (status: 0)
[2025-05-08 10:15:26] Email notification sent to user jdoe at john.doe@example.com
[2025-05-08 10:15:27] Used mail command to send email to admin@company.com (status: 0)
[2025-05-08 10:15:27] Email notification sent to admin at admin@company.com
[2025-05-08 10:15:28] Script completed successfully
```

## Security Considerations

- Run the script as root or with sudo privileges
- Store the configuration file with restricted permissions:
  ```bash
  chmod 600 .iam_config.env
  ```
- Use a secure channel to distribute the script
- Consider changing the default temporary password
- Use TLS/SSL for email communication (especially with Google SMTP)
- Monitor the log file for any suspicious activity
- Consider using app-specific passwords for Google authentication

## Troubleshooting

### Common Issues

1. **Permission denied**
   - Ensure the script is run with root privileges
   ```bash
   sudo ./iam_challenge
   ```

2. **User already exists**
   - Check the log file for details
   - Remove or update the user entry in the CSV

3. **Email notification failures with Postfix**
   - Check if Postfix service is running: `sudo systemctl status postfix`
   - Verify mail queue: `mailq`
   - Check mail logs: `sudo tail -f /var/log/mail.log`
   - Test local mail delivery: `echo "Test" | mail -s "Test" root`

4. **CSV format errors**
   - Ensure the CSV has the correct headers
   - Check for special characters or commas in fields (use quotes if necessary)

### Debug Email Issues

To test your Postfix mail setup:

```bash
echo "Test email body" | mail -v -s "Test Subject" recipient@example.com
```

Check mail logs:
```bash
sudo tail -f /var/log/mail.log
```

Check mail queue:
```bash
mailq
```

Flush mail queue:
```bash
sudo postfix flush
```

## Customization

### Custom Email Templates

Edit the `send_email_notification()` function to customize email templates:

```bash
nano iam_challenge
```

Find the `send_email_notification()` function and modify the `user_body` and `admin_body` variables.

### Additional User Properties

Extend the CSV format and script to handle additional user properties:
- Shell preferences
- SSH key setup
- User quotas
- Additional group memberships

## Advanced Usage

### Integration with Directory Services

The script can be extended to integrate with LDAP or Active Directory:

```bash
# Example LDAP addition (conceptual)
ldapadd -x -D "cn=admin,dc=example,dc=com" -w password -f user.ldif
```

### Batch Processing

For scheduled batch processing, add to crontab:

```bash
# Run every day at 6 AM
0 6 * * * /path/to/iam_challenge -f /path/to/daily_users.csv -e >> /var/log/iam_cron.log 2>&1
```

### Automation with CI/CD

Add to your GitHub Actions workflow:

```yaml
name: User Provisioning

on:
  schedule:
    - cron: '0 0 * * *'
  workflow_dispatch:

jobs:
  provision:
    runs-on: self-hosted
    steps:
      - uses: actions/checkout@v3
      - name: Run IAM Setup
        run: |
          sudo ./iam_challenge -f users.csv -e
```

## Docker Support

A Dockerfile is available to containerize the script:

```dockerfile
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    mailutils \
    mutt \
    && rm -rf /var/lib/apt/lists/*

COPY iam_challenge /usr/local/bin/
RUN chmod +x /usr/local/bin/iam_challenge

COPY .iam_config.env /etc/iam/.iam_config.env

ENTRYPOINT ["/usr/local/bin/iam_challenge", "-c", "/etc/iam/.iam_config.env"]
```

Build and run:
```bash
docker build -t iam-setup .
docker run -v /path/to/users.csv:/data/users.csv \
           -v /path/to/logs:/logs \
           iam-setup -f /data/users.csv -l /logs/iam.log -e
```

## Author and Contribution

Script created by Ishmael Gyamfi (May 3, 2025)

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License

Copyright (c) 2025 Ishmael Gyamfi

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.