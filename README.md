#!/bin/bash

# Print "GF-Tools" using figlet
echo "$(figlet GF-Tools)"

# Prompt for input file
read -p "Enter the path to the input file (e.g., aliveurl.txt): " INPUT_FILE

# Check if the file exists
if [ ! -f "$INPUT_FILE" ]; then
  echo "Error: File '$INPUT_FILE' not found!"
  exit 1
fi

# Output directory
OUTPUT_DIR="./gf-result"

# Create the output directory if it doesn't exist
mkdir -p "$OUTPUT_DIR"

# Define available patterns and their corresponding output files
declare -A PATTERNS=(
  ["sqli"]="sqli.txt"
  ["xss"]="xss.txt"
  ["ssrf"]="ssrf.txt"
  ["cors"]="cors.txt"
  ["redirect"]="redirect.txt"
  ["lfi"]="lfi.txt"
  ["idor"]="idor.txt"
  ["rce"]="rce.txt"
  ["upload-fields"]="upload-fields.txt"
)

# Display options to the user
echo "Available patterns:"
for pattern in "${!PATTERNS[@]}"; do
  echo "  - $pattern"
done

# Ask the user whether they want to process all patterns or choose specific ones
read -p "Do you want to process all patterns (type 'all') or type 'N/n' for specific patterns? " RESPONSE

# If the user types 'all', process all patterns
if [[ "$RESPONSE" == "all" ]]; then
  echo "[*] Processing all patterns..."
  SELECTED_PATTERNS="${!PATTERNS[@]}"  # Set SELECTED_PATTERNS to all available patterns
# If the user types 'N' or 'n', prompt for specific patterns
elif [[ "$RESPONSE" =~ ^[Nn]$ ]]; then
  read -p "Enter the patterns you want to process (space-separated, e.g., 'sqli xss'): " SELECTED_PATTERNS
else
  echo "Invalid response. Exiting..."
  exit 1
fi

# Process each selected pattern
for pattern in $SELECTED_PATTERNS; do
  if [[ -v PATTERNS[$pattern] ]]; then
    OUTPUT_FILE="$OUTPUT_DIR/${PATTERNS[$pattern]}"
    echo "[*] Processing pattern: $pattern -> $OUTPUT_FILE"
    cat "$INPUT_FILE" | gf "$pattern" > "$OUTPUT_FILE"
  else
    echo "Warning: Pattern '$pattern' is not recognized. Skipping..."
  fi
done

echo "[*] Selected patterns processed. Results saved in $OUTPUT_DIR"
