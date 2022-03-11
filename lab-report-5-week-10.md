# Lab Report 5

## Running `commonmark-spec` tests

I used a bash for loop (below) to run the 652 tests from CommonMark and manually looked for any discepancies between the output of my implementation and the provided implementation.

`script.sh`:

```bash
for file in test-files/*.md;
do
  echo "$file:"
  java MarkdownParse $file
done

```

I ran `make test` and `bash script.sh` in the local version of my repo and the provided repo on `ieng6`. 

## Test 1 (`149.md`)

I expected my implementation to have the output below (no links). This is a bit different than the provided repo output because I'm not printing the file name followed by an `"="` before the list of links. However, the output of Joe's repo is correct, since it indicates no links were found in `149.md`. This test file contains `**a<http://foo.bar/?q=**>`, which is not a link. 

Expected output: 

```bash
test-files/149.md:
[]
```

My repo output:

```bash
test-files/149.md:
[okay.]
```

Provided repo output:

```bash
test-files/149.md:
{test-files/149.md=[]}
```

My implementation relies on the index of periods in the Markdown file to parse links, so all words next to a period would be valid links. The issue with this approach is when a period is not part of a link (in this case, `149.md` contains the string `"okay."`). My code should not rely on periods to find links (instead, find brackets/parentheses).

Code to fix:

```java
ArrayList<Integer> periodList = new ArrayList<Integer>();
int currentIndex = 0;
while(currentIndex < markdown.length()) {
    int nextPeriodIndex = markdown.indexOf(".", currentIndex + 1);
    if(nextPeriodIndex != -1) {
        periodList.add(nextPeriodIndex);
        currentIndex = nextPeriodIndex;
    } else {
        break;
    }
}
for(int periodIndex: periodList) {
    int startIndex = periodIndex;
    int endIndex = periodIndex;
    ...
}
```

## Test 2 (`500.md`)

I expected my implementation to have the output below (no links). The output of Joe's repo is correct for this test file, since it includes it indicates there are no valid links.

Expected output:

```bash
test-files/479.md:
[]
```

My code output:

```bash
test-files/479.md:
Exception in thread "main" java.lang.StringIndexOutOfBoundsException: begin -1, end 25, length 26
    at java.base/java.lang.String.checkBoundsBeginEnd(String.java:3756)
    at java.base/java.lang.String.substring(String.java:1902)
    at MarkdownParse.getLinks(MarkdownParse.java:52)
    at MarkdownParse.main(MarkdownParse.java:60)
```

Provided repo output:

```bash
test-files/479.md:
{test-files/479.md=[]}
```
