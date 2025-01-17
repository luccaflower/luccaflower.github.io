---
title: "we do a little data wrangling"
description: "The linux command line is quite powerful!"
date: git Last Modified
tags:
  - journal
  - jobs
  - data-wrangling
---
I wanted to do some rudimentary statistics on the Swedish tech job market, both to test a few hypotheses of mine, and also to get informed about what subject areas I want to focus on in terms of employability. It's no secret that I want a new job doing something else than what I'm doing at my current job, so as much as I want to study for the sake of learning things that make me excited, I also need to stay cognizant of what job qualifications are in demand.

Luckily, the Swedish Employment publishes an open web API which gives access to all job adverts that they have ever hosted on their current platform. So let's get all job adverts that were posted since the beginning of 2024:
```bash
❯ curl -H "Content-type: application/json" \
'https://jobstream.api.jobtechdev.se/stream?date=2024-01-01T00:00:00' > jobs.json

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  682M    0  682M    0     0  4473k      0 --:--:--  0:02:36 --:--:-- 5017k
❯
```
Awesome.

Now, the Linux command line, it turns out, is an *excellent* too for wrangling large datasets. Let's look at the tools we're gonna use:
* **cat**: simply prints the contents of a file
* **jq**: format, filter, and transform JSON data.
* **sed**: short for "stream-editor". Can do various line-by-line transformations using RegEx. We're just gonna do simple search-replace with it.
* **sort**: it sorts!
* **uniq**: prints and counts unique lines. Requires sorted data.
* **grep**: extract data with RegEx!

The data we get is an array of JSON objects. The fields we're interested in are `occupation_group`, which denotes the job category, and `description`, which contains the job description. The `occupation_group`-field looks like this:
```json
{
  "concept_id": string,
  "label": string,
  "legacy_ams_taxonomy_id": int
}
```
We're interested in the `concept_id` for the label "Mjukvaru- och systemutvecklare m.fl." ("Software- and system developers etc.").

As a fun little exercise,  let's find the amount of job ads for each category:
```bash
❯ cat jobs.json |
jq '.[].occupation_group | select(.label != null) | .label' |
sort | uniq -c | sort -nr
   1879 "Mjukvaru- och systemutvecklare m.fl."
   1637 "Grundutbildade sjuksköterskor"
   1628 "Personliga assistenter"
   1392 "Företagssäljare"
    920 "Undersköterskor, hemtjänst, hemsjukvård, äldreboende och habilitering"
    915 "Motorfordonsmekaniker och fordonsreparatörer"
    ...
```
So much for the recession in the tech industry, huh.

Enough of that, let's find that ID:
```bash
cat jobs.json |
jq '.[].occupation_group | select(.label != null)' |
grep Mjukvaru -B2 -A2
{
  "concept_id": "DJh5_yyF_hEM",
  "label": "Mjukvaru- och systemutvecklare m.fl.",
  "legacy_ams_taxonomy_id": "2512"
}
--
{
  "concept_id": "DJh5_yyF_hEM",
  "label": "Mjukvaru- och systemutvecklare m.fl.",
  "legacy_ams_taxonomy_id": "2512"
}
--
#...
```
Now we can use that ID to filter for software jobs only, and extracting the formatted text of the advert:
```bash
jq '.[] | select(.occupation_group.concept_id == "DJh5_yyF_hEM") | .description.text_formatted'
```
Make it all lowercase for ease of processing keywords:
```bash
 tr '[:upper:]' '[:lower:]'
```
Remove HTML tags and newlines:
```bash
sed 's/\\n//g' | sed -E 's/<\/?[a-z]{1,10}>//g'
```
Extract all the keywords I'm interested in - programming languages and other qualifications. I use the -P flag to use Perl's RegEx engine, which enables lookahead and lookbehind qualifiers.
```bash
grep -oP '\(?<ǃ\w)((c#)|(c)|(java)|(javascript)|(typescript)|(js)|(ts)|(python)|(linux)|(elixir)|(erlang)|(go)|(c\+\+)|(react)|(angular)|(svelte)|(\.net)|(embedded)|(rust)|(scala)|(clojure)|(kotlin)|(ruby)|(swift)|(golang))(?=[ ,./])'
```
Count the occurrence of each keyword
```bash
sort | uniq -c | sort -nr
```
Putting it all together
```bash
❯ cat jobs.json |
jq '.[] | select(.occupation_group.concept_id == "DJh5_yyF_hEM") | .description.text_formatted' |
tr '[:upper:]' '[:lower:]' |
sed 's/\\n//g' | sed -E 's/<\/?[a-z]{1,10}>//g' |
grep -oP '(?<!\w)((c#)|(c)|(java)|(javascript)|(typescript)|(js)|(ts)|(python)|(linux)|(elixir)|(erlang)|(go)|(c\+\+)|(react)|(angular)|(svelte)|(\.net)|(embedded)|(rust)|(scala)|(clojure)|(kotlin)|(ruby)|(swift)|(golang))(?=[ ,./])' |
sort | uniq -c | sort -nr
    583 java
    558 python
    508 .net
    487 embedded
    434 c++
    411 c#
    339 c
    327 react
    311 linux
    238 javascript
    217 typescript
    176 js
    165 angular
    107 go
    104 kotlin
     64 ts
     40 swift
     34 rust
     34 golang
     29 scala
     13 ruby
      4 svelte
      4 erlang
      2 elixir
      1 clojure
```
Very interesting. I expected Java to be high, but I honestly thought
C#/.NET would be higher. Same with the JS/TS + frameworks. Python is
also highly sought after, which is expected.

Now, Go also happens to be a common English word, and given that many of these ads are written in English, the likelyhood of Go being overcounted is very high. I'm surprised to see it rank so low, regardless.

What I'm most interested in is the amount of listings that contain the word "embedded". As mentioned in [a previous blog post](/blog/ossu-2024-review/), I've gotten quite fond of systems level programming. With the understanding that I'd need to learn some rudimentary electrical engineering, maybe I should take a shot at becoming an embedded developer. It sounds fun, not gonna lie.
