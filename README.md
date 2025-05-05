# Linux IAM Automation Scripts

> A comprehensive solution for automating Identity and Access Management (IAM) tasks in Linux environments using Bash scripts.

## 🔑 Overview

This repository contains Bash scripts designed to automate user and group management in Linux systems. It's particularly useful for system administrators who need to provision multiple user accounts with specific configurations, ensuring consistent application of security policies and permissions.

## 🎯 Features

### Core Features
- Automated user creation with specific attributes
- Group management
- Home directory creation with proper permissions
- Temporary password assignment
- Forced password change on first login
- Detailed activity logging

### Advanced Features
- Password complexity validation
- Email notifications for account creation
- Command-line argument support
- CSV file input parsing

## 📋 Repository Contents

- **`iam_setup.sh`**: Basic IAM automation script
- **`enhanced_iam_setup.sh`**: Advanced version with additional features
- **`users.txt`**: Sample user input file
- **`enhanced_users.txt`**: Extended sample user input file with email addresses
- **`iam_setup.log`**: Sample log file showing script execution output

## 🚀 Getting Started

### Prerequisites

- Linux environment (physical machine, VM, or WSL)
- Root privileges
- Basic understanding of Linux user management
- (Optional) `mail` command installed for email notifications

### Basic Usage

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/linux-iam-automation.git
   cd linux-iam-automation
   ```

2. **Make the scripts executable**
   ```bash
   chmod +x iam_setup.sh enhanced_iam_setup.sh
   ```

3. **Prepare your user input file**
   Create or modify `users.txt` with entries in the format:
   ```
   username,fullname,group
   ```

4. **Run the basic script**
   ```bash
   sudo ./iam_setup.sh
   ```

### Advanced Usage

The enhanced script offers more flexibility through command-line options:

```bash
sudo ./enhanced_iam_setup.sh [OPTIONS]
```

#### Available Options

| Option | Long Option | Description | Default |
|--------|-------------|-------------|---------|
| `-f`   | `--file`    | Specify input CSV file | `users.txt` |
| `-l`   | `--log`     | Specify log file | `iam_setup.log` |
| `-p`   | `--password`| Set temporary password | `ChangeMe123` |
| `-e`   | `--email`   | Enable email notifications | `false` |
| `-h`   | `--help`    | Display help message | - |

#### Examples

Run with a custom user file:
```bash
sudo ./enhanced_iam_setup.sh -f custom_users.csv
```

Enable email notifications:
```bash
sudo ./enhanced_iam_setup.sh -e
```

Specify all options:
```bash
sudo ./enhanced_iam_setup.sh -f custom_users.csv -l custom.log -p "SecureTemp123!" -e
```

## 📝 Input File Format

### Basic Format
```
username,fullname,group
```

Example:
```
jdoe,John Doe,engineering
asmith,Alice Smith,engineering
mjones,Mike Jones,design
```

### Enhanced Format (with Email)
```
username,fullname,group,email
```

Example:
```
jdoe,John Doe,engineering,jdoe@company.com
asmith,Alice Smith,engineering,asmith@company.com
mjones,Mike Jones,design,mjones@company.com
```

## 🔍 Script Details

### `iam_setup.sh`

This script handles the core functionality of user and group management:

1. Reads the input file line by line
2. Creates groups if they don't exist
3. Creates users with specified attributes
4. Sets temporary passwords
5. Forces password change on first login
6. Sets appropriate permissions on home directories
7. Logs all actions with timestamps

### `enhanced_iam_setup.sh`

Extends the basic script with advanced features:

1. Command-line argument parsing
2. Password complexity validation
3. Email notifications
4. More detailed error handling
5. Enhanced logging

## 📊 Log File

The scripts generate detailed logs of all operations. Example log entries:

```
# IAM Setup Log - Started on Sat May 3 10:15:32 EDT 2025
[2025-05-03 10:15:32] Script started
[2025-05-03 10:15:32] Processing user: jdoe (John Doe) for group: engineering
[2025-05-03 10:15:32] Created group: engineering
[2025-05-03 10:15:33] Created user: jdoe with home directory
[2025-05-03 10:15:33] Set temporary password for jdoe
[2025-05-03 10:15:33] Password change required at next login for jdoe
[2025-05-03 10:15:33] Set permissions 700 on /home/jdoe
```

## ⚙️ Testing

To verify the script worked correctly:

1. **Check user creation**
   ```bash
   grep username /etc/passwd
   ```

2. **Verify group creation**
   ```bash
   grep groupname /etc/group
   ```

3. **Check home directory permissions**
   ```bash
   ls -la /home/username
   ```

4. **Verify password expiration policy**
   ```bash
   chage -l username
   ```

## 🔒 Security Considerations

- **Password Handling**: The scripts use system calls for password setting and never store passwords in plain text
- **Directory Permissions**: Home directories are set to 700 (user-only access)
- **Password Policies**: Users are forced to change their password on first login
- **Error Checking**: The scripts validate operations to ensure security settings are applied correctly

## 🛠️ Customization

### Password Policy

You can modify the password complexity requirements in the `check_password_complexity` function of the enhanced script:

```bash
# Function to check password complexity
check_password_complexity() {
    local password="$1"
    
    # At least 8 characters
    if [ ${#password} -lt 8 ]; then
        return 1
    fi
    
    # Add or modify additional checks here
    ...
}
```

### Email Notifications

To customize email content, modify the `send_email_notification` function:

```bash
send_email_notification() {
    ...
    local body="Hello $fullname,
    
    # Customize email content here
    ...
    "
    ...
}
```

## 🔍 Troubleshooting

### Common Issues

1. **Permission Denied**
   - Make sure you're running the script with sudo or as root

2. **User Already Exists**
   - The script will log this and continue with the next user

3. **Email Notifications Not Working**
   - Ensure the `mail` command is installed
   - Check that your system can send emails

### Debugging

- Check the log file for detailed error messages
- Run the script with more verbose output:
  ```bash
  sudo bash -x iam_setup.sh
  ```

## 📚 Best Practices

1. **Always review the input file** before running the script in production
2. **Test in a non-production environment** first
3. **Backup your system** before making bulk user changes
4. **Review logs** after script execution
5. **Use strong temporary passwords** that meet your organization's requirements

## 🔄 Contributing

Contributions are welcome! Here's how you can contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

Please make sure your code follows best practices for bash scripting and includes appropriate error handling.

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 👥 Authors

- **Your Name** - *Initial work*

## 🙏 Acknowledgments

- Linux documentation for user management commands
- The bash scripting community for best practices and examples

---

*This project was created as part of a Linux system administration automation challenge.*