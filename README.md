# FILE-INTEGRITY-CHECKER


import hashlib
import os
import json
import time

HASH_FILE = "file_hashes.json"
MONITOR_INTERVAL = 30  # Time in seconds between checks

def calculate_hash(file_path):
    """Calculate SHA-256 hash of a file."""
    hasher = hashlib.sha256()
    try:
        with open(file_path, 'rb') as f:
            while chunk := f.read(4096):
                hasher.update(chunk)
        return hasher.hexdigest()
    except FileNotFoundError:
        return None

def scan_directory(directory):
    """Scan directory and compute hashes for all files."""
    file_hashes = {}
    if not os.path.exists(directory) or not os.path.isdir(directory):
        print("Error: Invalid directory path or directory does not exist.")
        return file_hashes
    for root, _, files in os.walk(directory):
        for file in files:
            file_path = os.path.join(root, file)
            file_hashes[file_path] = calculate_hash(file_path)
    return file_hashes

def load_previous_hashes():
    """Load previously stored hashes from JSON file."""
    if os.path.exists(HASH_FILE):
        with open(HASH_FILE, 'r') as f:
            return json.load(f)
    return {}

def save_hashes(file_hashes):
    """Save current file hashes to JSON file."""
    with open(HASH_FILE, 'w') as f:
        json.dump(file_hashes, f, indent=4)

def detect_changes(directory):
    """Detect file changes by comparing current and previous hashes."""
    old_hashes = load_previous_hashes()
    new_hashes = scan_directory(directory)
    if not new_hashes:
        return
    
    added = [file for file in new_hashes if file not in old_hashes]
    removed = [file for file in old_hashes if file not in new_hashes]
    modified = [file for file in new_hashes if file in old_hashes and new_hashes[file] != old_hashes[file]]
    
    if added or removed or modified:
        print("Changes detected:")
        if added:
            print("  Added:", added)
        if removed:
            print("  Removed:", removed)
        if modified:
            print("  Modified:", modified)
    else:
        print("No changes detected.")
    
    save_hashes(new_hashes)

def monitor_directory(directory):
    """Continuously monitor the directory for changes."""
    if not os.path.exists(directory) or not os.path.isdir(directory):
        print("Error: Directory does not exist or is not accessible.")
        return
    print(f"Monitoring directory: {directory} (Press Ctrl+C to stop)")
    while True:
        detect_changes(directory)
        time.sleep(MONITOR_INTERVAL)

if __name__ == "__main__":
    import sys
    
    if len(sys.argv) > 1:
        directory_to_monitor = sys.argv[1].strip()
    else:
        print("Error: No directory provided. Please provide a directory as a command-line argument.")
        sys.exit(1)
    
    monitor_directory(directory_to_monitor)
