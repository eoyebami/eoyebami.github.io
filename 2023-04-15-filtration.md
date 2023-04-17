Filtering linux content

Grep: allows you to filter through files for specific patterns or phrases
```  
  -v: does a reverse filter
  -i: filters ignoring case-sensitivity
  -r: filters through files of a directory
  -e: will allow you to specify multiple phrases to grep for
  -l: lists the files that contain the grepped value
  -c: lists the amount of times the phrases comes up
  -A: specifies the number of lines, after value grepping for, to also display (after-context)
  -B: specifies the number of lines, before the value grepping for, to also display (before-context)
  -C: specifies the number of lines, before and after the value grepping for, to also display 
```

ex:
```
  grep -vr zip (will do a reverse grep within the directory for the word zip)
  grep -i first *.txt (will grep the word "first" in all .txt files, ignoring case sensitivity)
  grep -e "first" -e "last" *.txt (will grep for the word "first" and "last" in all .txt files)
  grep "first" -A 2 file1 (will grep the word "first" and the 2 lines beneath it, in file1)
  grep ex -A 2 *.txt (will grep the pattern "ex" and the 2 lines beneath it, from all .txt files)
  grep ex -B 2 *.txt (will grep the pattern "ex" and the 2 lines above it, from all .txt files)
  grep ex -C 2 *.txt (will grep the pattern "ex" and the 2 lines above and beneath it, from all .txt files)
  grep -e "ex" -C 2 -e "grep" *.txt (will grep for both the phrases "ex" and "grep" from all .txt files, while also displaying 2 lines above and below the grepped values)
  grep -le "ex" -C 2 -e "grep" *.txt (will list all .txt files that contain the grepped values "ex" and "grep")
  grep -rce "ex" -e "grep" (will list the amount of times the grepped phrases "ex" and "grep" come up within the directory)
```

Extras:
```
  $: will grep for lines that end with specific pattern
  ^: will grep for lines that start with a specific pattern
```

ex:
```
  cat /var/log/messages | grep "^Set 26 03:26:41" (will grep for lines in file that start with grepped pattern)
  grep "end$" filex (will grep for lines in file that end with grepped pattern)
```
