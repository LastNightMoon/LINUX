find . -type f -name "data*.txt" -exec dirname {} \; | sort -u
