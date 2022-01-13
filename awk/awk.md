# awk

```sh
# Print first column
ps | awk '{print $1}'

# Print second column
ps | awk '{print $2}'

# Print frirst column using ":" as the field seperator 
cat /etc/passwd | awk -F ":" '{print $1}'
# or 
awk -F ":" '{print $1}' /etc/passwd

# Print multiple columns
awk -F ":" '{print $1 $2 $3}' /etc/passwd 
# with "," seperator  
awk -F ":" '{print $1","$2","$3}' /etc/passwd
# or
awk 'BEGIN{FS=":"; OFS="-"} {print $1,$2,$3}' /etc/passwd

# Swap 1st and 3rd columns
awk 'BEGIN{FS=":"; OFS=","} {print $3,$2,$1}' /etc/passwd

# using awk to do something you might do with sed
# awk '/some-expression/'
# Search for [//] all lines that begin with [^] the string "/bin" then print the last column [$NF]
cat /etc/shells | awk -F "/" '/^\/bin/ {print $NF}'

# for good measure you want to remove the duplicates and create a sorted list
cat /etc/shells | awk -F "/" '/^\/bin/ {print $NF}' | uniq | sort

# How to add column heading and fix the column width
printf "111|222|333\naaa|bbb|ccc\nxxx|yyy|zzz\n" | awk -F "|" '
  BEGIN{
    format = "%-15s %-15s %-15s\n";
    printf format, "First","Second","Third"
  }
  {
    printf format, $1,$2,$3
  }'
```
