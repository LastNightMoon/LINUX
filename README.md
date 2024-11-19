find . -type f -name "*.txt" -exec du -b {} + | awk '{sum += $1} END {print sum}'
