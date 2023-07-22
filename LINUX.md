![](https://hackmd.io/_uploads/HJGWa9F53.png)

### ls

`ls -i` look for file index

### find

`find -type f -name *.conf -size +20k -newermt 2020-03-03`

```go
| Options             | Description                                                                         |
| ------------------- | ----------------------------------------------------------------------------------- |
| -type f             | define the type                                                                     |
| ------------------- | -------------------------------------------------------------------                 |
| -name *.conf        | indicate the name of the file we are looking for. The asterisk (*) stands for 'all' |
| ------------------- | -------------------------------------------------------------------                 |
| -size +20k          | filter larger than 20 KiB                                                           |
| ------------------- | -------------------------------------------------------------------                 |
| -newermt 2020-03-03 | set the date. Only files newer than the specified date will be presented.                                                           |
| ------------------- | -------------------------------------------------------------------                 |
|  2>/dev/null        | STDERR redirection to the 'null device'
```

### FD

```GO!
Data Stream for Input
STDIN – 0

Data Stream for Output
STDOUT – 1

Data Stream for Output that relates to an error occurring.
STDERR – 2
```

- `find -type f -name *.conf 2> stderr.txt 1>stdout.txt` 

- `apt -qq list --installed | wc -l`

(wc = word count, -l = line)

netstat -ln4 | grep LISTEN | grep -v 127 | wc -l
