#bash #for

## FOR

``` bash
for VARIABLE in 1 2 3 4 5 .. N
do
	command1
done
```

```bash
for VARIABLE in file1 file2 file3
do
    command1 on $VARIABLE
done
```

```bash
for var in list; do command1; done
for var in "${!list[@]}"; do command1; done
for i in {1..5}; do COMMAND-HERE; done
for((i=1;i<=10;i+=2)); do echo "Welcome $i times"; done
```

## WHILE
#while
```bash
while read line
do
	echo "Line #$count: $line"
	count=$(( $count + 1 ))
done
```