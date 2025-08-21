# image-renamer
My first repository.
import os
import re
from pathlib import Path

# --- Configuration ---
input_dir = "product_images"   # Folder containing the images
output_format = "{code}_{color}_{view}.jpg"  # Desired final pattern

# Regular expressions for common patterns
patterns = [
    re.compile(r"(?P<view>[a-zA-Z]+)[-_](?P<color>[a-zA-Z]+)[-_](?P<code>[a-zA-Z0-9]+)", re.IGNORECASE),
    re.compile(r"(?P<code>[a-zA-Z0-9]+)[-_](?P<view>[a-zA-Z]+)[-_](?P<color>[a-zA-Z]+)", re.IGNORECASE),
    re.compile(r"(?P<color>[a-zA-Z]+)[-_](?P<code>[a-zA-Z0-9]+)[-_](?P<view>[a-zA-Z]+)", re.IGNORECASE)
]

# --- Function to standardize filenames ---
def standardize_filename(filename: str):
    name, ext = os.path.splitext(filename)
    ext = ext.lower()
    for pattern in patterns:
        match = pattern.match(name)
        if match:
            parts = match.groupdict()
            return output_format.format(**parts), ext
    return None, ext  # Pattern not matched

# --- Process all image files ---
def rename_files(directory):
    renamed_count = 0
    for file in os.listdir(directory):
        if not file.lower().endswith(('.jpg', '.jpeg', '.png', '.webp')):
            continue
        old_path = os.path.join(directory, file)
        new_name, ext = standardize_filename(file)
        if new_name:
            new_file = os.path.join(directory, new_name)
            new_file = os.path.splitext(new_file)[0] + ".jpg"  # Normalize to .jpg
            os.rename(old_path, new_file)
            print(f"Renamed: {file} -> {os.path.basename(new_file)}")
            renamed_count += 1
        else:
            print(f"Skipped (pattern not matched): {file}")
    print(f"\n✅ Total renamed files: {renamed_count}")

# --- Run the script ---
if __name__ == "__main__":
    Path(input_dir).mkdir(parents=True, exist_ok=True)
    rename_files(input_dir)
