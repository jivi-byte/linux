# AWK Cheatsheet

## AWK: Special Variable Notes

- `FILENAME` - References the current input file.
- `FS` - The current field separator used to denote each field in a record. By default, this is set to whitespace.
- `NF` - The number of fields in the current record.
- `NR` - The row number of the current record.
- `OFS` - The field separator for the output data. By default, this is set to whitespace.
- `ORS` - The record separator for the output data. By default, this is a newline character.
- `RS` - The record separator used to distinguish separate records in the input file. By default, this is a newline character.
- `FNR` - References the number of the current record relative to the current input file.
  - For instance, if you have two input files, this tells you the record number for each file instead of the total record count.

## AWK: Printing

### Version and basic printing

```bash
awk --version
awk '{print}' filename
awk 'NF>0' filename
```

- `awk 'NF>0' filename` prints only non-blank lines.

### User and column output

```bash
awk 'BEGIN {print ENVIRON["USER"]}'
awk 'BEGIN {print ENVIRON["USER"]; exit}'
awk '{print $3 "\t" $4}' filename
```

### Line and field counts

```bash
awk 'END { print NR }' filename
awk 'NR==1 {print NF}' filename
```

### Record selection

```bash
ls -l | awk '{print $5}' | sort -nr
awk 'NR==8,NR==12' filename
awk 'NR==52 { print; exit }' filename
awk 'NR==52' filename
awk 'NR%2 == 0' filename
awk 'NR%2 == 1' filename
```

### Head and tail emulation

```bash
awk 'NR > 1 { exit }; 1' filename
awk '{ y=x "\n" $0; x=$0 } END { print y }' filename
awk '{array[NR]=$0} END {for (i=NR-2; i<= NR; i++) print array[i]}' filename
awk 'NR < 11' filename
awk '1; NR == 10 { exit }' filename
awk 'NR==1, NR==10' filename
awk 'END { print }' filename
awk '{ rec=$0 } END{ print rec }' filename
awk 'END{ print $0}' filename
```

### Other printing tricks

```bash
awk 'END {print FILENAME}' filename
awk '{ print $2, $1 }' filename
cat filename | awk '{print $2 " " print $1}'
awk '{ for (i=NF; i>0; i--) printf("%s ", $i); printf ("\n") }' filename
awk '{ print NF ":" $0 }' filename
awk '{ print $NF }' filename
awk '{ a[i++] = $0 } END { for (j=i-1; j>=0;) print a[j--] }' filename
awk '/\\$/ { sub(/\\$/,"" ); getline t; print $0 t; next }; 1' filename
awk '{ print NR "\t" $0 }' filename
awk 'NF { $0=++a " :" $0 }; { print }' filename
awk '{ s = 0; for (i = 1; i <= NF; i++) s = s+$i; print s }' filename
awk '{ for (i = 1; i <= NF; i++) s = s+$i }; END { print s+0 }' filename
awk 'BEGIN {FS = ")"} ; {print $2}' filename
awk '{ temp = $1; $1 = $2; $2 = temp; print }' filename
```

## AWK: Search & Print

```bash
awk 'BEGIN {IGNORECASE=1} /pattern/' filename
awk '$5 == "abc123"' filename
awk '$5 == "abc123" {print "Row-Number:- ", NR, $0}' filename
awk '/pattern/,0' filename
awk '/pattern/ {++cnt} END {print "COUNT=" cnt}' filename
awk '/pattern/ {print NR}' filename
awk '/pattern/ {print FILENAME}' *
awk '/pattern/ {print FILENAME, FNR;}' *
awk -F 'pattern' '{print NF-1}' filename
awk '/^UC occurrence/' filename
awk 'END  {print $4}' filename
awk '/ A2MCXX006534SW$/' filename
awk '/pattern/ {print $0}' filename
awk '/^UUID/ {print $1;}' filename
awk '/AAA|BBB|CCC/' filename
awk '/AAA.*BBB.*CCC/' filename
awk '$7  ~ /^[a-f]/' filename
awk '/pattern/ { print x }; { x=$0 }' filename
awk '/pattern/ { getline; print }' filename
awk '/startpattern/,/endpattern/' filename
awk '$2 ~ /^pattern/' filename
awk '$2 !~ /^pattern/' filename
awk '$2 !~ /^tti/ && $1 < 5' filename
awk 'length > 65' filename
awk 'length($0) > 65' filename
```

## AWK: File Manipulation

```bash
awk '{ sub(/^[ \t]+/, ""); print }' filename
awk '{$1=$1; print}' filename
awk '{ sub(/[ \t]+$/, ""); print }' filename
awk '{ gsub(/^[ \t]+|[ \t]+$/, ""); print }' filename
awk '{ $1=$1; print }' filename
awk '{ sub(/^/, "     "); print }' filename
awk '{ printf "%79s\n", $0 }' filename
awk '{ l=length(); s=int((79-l)/2); printf "%"(s+l)"s\n", $0 }' filename
awk '{ sub(/foo/,"bar"); print }' filename
awk '/baz/ { gsub(/foo/, "bar") }; { print }' filename
awk '!/baz/ { gsub(/foo/, "bar") }; { print }' filename
awk '{ gsub(/scarlet|ruby|puce/, "red"); print }' filename
awk 'a != $0; { a = $0 }' filename
awk '!a[$0]++' filename
awk 'ORS=NR%5?",":"\n"' filename
awk '{ $2 = ""; print }' filename
awk NF filename
awk '/./' filename
```

## AWK: Assignment Operator

```bash
awk -v name=jerry 'BEGIN {print name}'
```

## AWK: Concatenation Operator

```bash
awk 'BEGIN { str1="Hello, "; str2="World"; str3 = str1 str2; print str3 }'
```

Output:

```text
Hello World
```

## AWK: Arithmetic Operators

```bash
awk 'BEGIN { a = 50; b = 20; print "(a + b) = " (a + b) }'
awk 'BEGIN { a = 50; b = 20; print "(a - b) = " (a - b) }'
awk 'BEGIN { a = 50; b = 20; print "(a * b) = " (a * b) }'
awk 'BEGIN { a = 50; b = 20; print "(a / b) = " (a / b) }'
awk 'BEGIN { a = 50; b = 20; print "(a % b) = " (a % b) }'
```

## AWK: Shorthand Operations

```bash
awk 'BEGIN { cnt=10; cnt += 10; print "Counter =", cnt }'
awk 'BEGIN { cnt=100; cnt -= 10; print "Counter =", cnt }'
awk 'BEGIN { cnt=10; cnt *= 10; print "Counter =", cnt }'
awk 'BEGIN { cnt=100; cnt /= 5; print "Counter =", cnt }'
awk 'BEGIN { cnt=100; cnt %= 8; print "Counter =", cnt }'
awk 'BEGIN { cnt=2; cnt ^= 4; print "Counter =", cnt }'
awk 'BEGIN { cnt=2; cnt **= 4; print "Counter =", cnt }'
```

## AWK: Logical Operators

```bash
awk 'BEGIN {num = 5; if (num >= 0 && num <= 7) printf "%d is in octal format\n", num }'
awk 'BEGIN {ch = "\n"; if (ch == " " || ch == "\t" || ch == "\n") print "Current char is whitespace." }'
awk 'BEGIN { name = ""; if (! length(name)) print "name is empty string." }'
awk 'BEGIN { a = 10; b = 20; (a > b) ? max = a : max = b; print "Max =", max}'
awk 'BEGIN { a = -10; a = +a; print "a =", a }'
awk 'BEGIN { a = -10; a = -a; print "a =", a }'
awk 'BEGIN { a = 10; a = a ^ 2; print "a =", a }'
awk 'BEGIN { a = 10; a = a ** 2; print "a =", a }'
```

- `&&` - AND
- `||` - OR
- `!` - NOT
- ternary operator example
- unary `+` and unary `-`
- exponentiation examples

## AWK: Relational Operators

```bash
awk 'BEGIN { a = 10; b = 10; if (a == b) print "a == b" }'
awk 'BEGIN { a = 10; b = 10; if (a == b) print "a == b"; else print "a != b"}'
awk 'BEGIN { a = 10; b = 20; if (a != b) print "a != b" }'
awk 'BEGIN { a = 10; b = 20; if (a != b) print "a != b"; else print "a == b" }'
awk 'BEGIN { a = 10; b = 20; if (a < b) print "a < b" }'
awk 'BEGIN { a = 10; b = 20; if (a < b) print "a < b"; else print "a > b" }'
awk 'BEGIN { a = 10; b = 10; if (a <= b) print "a <= b" }'
awk 'BEGIN { a = 10; b = 10; if (a <= b) print "a <= b"; else print "a > b" }'
awk 'BEGIN { a = 10; b = 20; if (b > a ) print "b > a" }'
awk 'BEGIN { a = 10; b = 20; if (b > a ) print "b > a"; else print "a > b" }'
awk 'BEGIN { a = 10; b = 10; if (a >= b) print "a >= b" }'
awk 'BEGIN { a = 10; b = 10; if (a >= b) print "a >= b"; else print "a < b" }'
```

## AWK: Regular Expressions

```bash
awk '$0 ~ 9' marks.txt
awk '$0 !~ 9' marks.txt
```

```bash
echo -e "cat \nbat \nmat \nrat" | awk '/b.t/'
```
- `.` matches exactly one character.

```bash
echo -e "This\nThat\nThere\nthese" | awk '/^The/'
```
- `^` matches the start of a line.

```bash
echo -e "knife\nknow\nfun\nfin\nfan\nnine" | awk '/n$/'
```
- `$` matches the end of a line.

```bash
echo -e "Call\nTall\nBall" | awk '/[CT]all/'
```
- character set example.

```bash
echo -e "Call\nTall\nBall" | awk '/[^CT]all/'
```
- exclusive character set example.

```bash
echo -e "Call\nTall\nBall\nSmall\nShall" | awk '/Call|Ball/'
```
- alternation example.

```bash
echo -e "Colour\nColor" | awk '/Colou?r/'
```
- `?` means zero or one occurrence.

```bash
echo -e "ca\ncat\ncatt" | awk '/cat*/'
```
- `*` means zero or more occurrences.

```bash
echo -e "111\n22\n123\n234\n456\n222" | awk '/2+/'
```
- `+` means one or more occurrences.

```bash
echo -e "Apple Juice\nApple Pie\nApple Tart\nApple Cake" | awk '/Apple (Juice|Cake)/'
```
- grouping example.

## AWK: Format Specifier

```bash
awk 'BEGIN { printf "ASCII value 65 = character %c\n", 65 }'
awk 'BEGIN { printf "Percentages = %d\n", 80.66 }'
awk 'BEGIN { printf "Percentages = %E\n", 80.66 }'
awk 'BEGIN { printf "Percentages = %e\n", 80.66 }'
awk 'BEGIN { printf "Percentages = %f\n", 80.66 }'
```
