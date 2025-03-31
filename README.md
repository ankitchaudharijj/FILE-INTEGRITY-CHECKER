## Description
 
 The **File Integrity Checker Tool** is a Python-based utility that helps ensure the integrity of files by continuously monitoring their hash values. It detects unauthorized or accidental modifications to files and notifies users through sequential pop-up alerts. The tool also provides an interactive interface for managing monitored files, allowing users to add, remove, or update file hashes as needed.
 
 ### Key Features:
 - **File Hash Monitoring**: Continuously monitors specified files for any modifications.
 - **Pop-up Notifications**: Alerts users about file changes with pop-ups for each modified file (with a 5-second delay between notifications).
 - **Interactive File Management**: Add, remove, or update monitored files via a CLI interface.
 - **JSON-Based Database**: Stores file paths, hashes, and timestamps for tracking integrity over time.
 - **Customizable**: Easily adjust monitoring intervals and notification delays.

 Main Code Parts
 
 ### 1. File Monitoring (`int.py`)
 
 This script monitors the integrity of files and alerts users about changes.
 
 ```python
 def check_file_integrity(database):
     for filepath, entries in database.items():
         last_saved_hash = entries[-1]["hash"]
         current_hash = calculate_file_hash(filepath)
         if current_hash is None:
             continue
         if current_hash != last_saved_hash:
             show_notification(filepath)
             time.sleep(5)  # Delay between notifications
             update_file_hash(filepath, current_hash, database)
 ```
 
 - **Functionality**: Monitors files for changes, notifies users, and updates the database with new hashes.
 - **Customizable Delay**: Pop-ups appear for each modified file with a 5-second delay.
 
 ### 2. File Management (`file.py`)
 
 This script provides a command-line interface for managing monitored files.
