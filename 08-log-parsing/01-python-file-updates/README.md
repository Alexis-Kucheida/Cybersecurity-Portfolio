# Python — Algorithm for File Updates (Allow List)

## Overview
In this activity, I built a Python algorithm to maintain an allow list of IP addresses used to control access to restricted patient-record systems. The goal was to remove IP addresses listed in a separate `remove_list` from the allow list stored in `allow_list.txt`. The script reads the file, parses its content into a list, removes unauthorized IPs, and writes the updated allow list back to the file.

## What I implemented
- Opened the allow list file safely using a `with open()` context manager
- Read file contents with `.read()` and parsed them into a list using `.split()`
- Iterated through `remove_list` and removed matching IPs from `ip_addresses` using an `if` condition + `.remove()`
- Converted the updated list back into a newline-separated string using `"\n".join()`
- Overwrote `allow_list.txt` with the revised allow list using `.write()` 

## Key Python concepts used
- File handling: `with open(import_file, "r")` and `with open(import_file, "w")`
- String/list conversion: `.read()`, `.split()`, `"\n".join(...)`
- Iteration + conditionals: `for element in remove_list:` + `if element in ip_addresses:`
- List updates: `.remove()` (valid here because the allow list contains no duplicates) 

