# Programming By Stealth - Challenges solutions

This folder contains my solutions to the challanges in the instalments for the [Programming By Stealth podcast](https://pbs.bartificer.net), starting from episode 146

## [Episode 146 of X — Bash: Shell Loops](https://pbs.bartificer.net/pbs146)

### Optional challenge

If you’d like to put your Bash skills to the test, try writing a script that accepts a a whole number as an input, either as the first argument or from a user prompt, then prints out the standard n-times multiplication tables to the screen, i.e., if you give the number 3, the output should be:

```Text
1 x 3 = 3
2 x 3 = 6
3 x 3 = 9
4 x 3 = 12
5 x 3 = 15
6 x 3 = 18
7 x 3 = 21
8 x 3 = 24
9 x 3 = 27
10 x 3 = 30
```

You should use the `bc` (basic calculator) terminal command to do the arithmetic. You’ll need to teach yourself how to use it either with the help of your favourite search engine, or the man page (`man bc`).

For bonus credit, update your script to allow the user to specify how high the table should go, defaulting to 10 like above.

### Solution

The solution is in the file `pbs146-challenge_solution.sh`. To test it, call it with a single argument (which has to be a positive integer number), like this:

```bash
./pbs146-challenge_solution.sh 3
```

which will have the requested output:

```Text
1 × 3 = 3
2 × 3 = 6
3 × 3 = 9
4 × 3 = 12
5 × 3 = 15
6 × 3 = 18
7 × 3 = 21
8 × 3 = 24
9 × 3 = 27
10 × 3 = 30
```

Adding a second argument (again a positive integer number) will change the upper limit of the table:

```bash
./pbs146-challenge_solution.sh 3 5
```

```Text
1 × 3 = 3
2 × 3 = 6
3 × 3 = 9
4 × 3 = 12
5 × 3 = 15
```

## [Episode 147 of X — Bash: Arrays](https://pbs.bartificer.net/pbs147)

### Optional challange

Write a script to take the user’s breakfast order.

The script should store the menu items in an array, then use a `select` loop to to present the user with the menu, plus an extra option to indicate they’re done ordering. Each time the user selects an item, append it to an array representing their order. When the user is done adding items, print their order.

For bonus credit, update your script to load the menu into an array from a text file containing one menu item per line, ignoring empty lines and lines starting with a `#` symbol.

### Solution

The solution is in the file `pbs147-challenge_solution.sh`. Went directly for the bonus credit version, so alongside the script there is also the menu definition text file `breakfast_menu.txt`.

The main difficulty was in filling the global array variable from the `while` loop, so used process redirection (`<()`) and an input redirection (`<`) at the end of the loop, like this.

```bash
while read -r line
do
	# ...
done < <(cat $menu_filename)
```

Credit to [this answer](https://askubuntu.com/questions/678915/whats-the-difference-between-and-in-bash) on StackOverflow.


## [Episode 148 of X — Bash: Potpourri (Subshells, Relative Paths & More)](https://pbs.bartificer.net/pbs148)

### Optional Challenge

Update the breakfast menu script to it can optionally accept an argument — a limit on the number of items you can order.

### Solution

The solution is in the file `pbs148-challenge_solution.sh`.

## [Episode 149 of X — Bash: Better Arguments with POSIX Special Variables and Options](https://pbs.bartificer.net/pbs149)

### Optional Challenge

Update the breakfast menu script so it accepts two options:

1) `-l LIMIT` where `LIMIT` is the maximum number of items that can be ordered, which must be a whole number greater than or equal to one
2) `-s` to request some snark

### Solution

Not a native speaker, so not sure how to handle "snark" in messages...

## [Episode 150 of X — Bash: Better Arguments with POSIX Special Variables and Options](https://pbs.bartificer.net/pbs150)

### Optional Challenge

Update your challenge solution from last time so it can ingest its menu in three ways:

1) Default to reading the menu from a file named `menu.txt` in the same folder as the script.
2) Read the menu from `STDIN` with the optional argument `-m -`.
3) Read the menu from a file with the optional argument `-m path_to_file.txt`

Regardless of where the menu is coming from, always present the menu interactively, i.e., the user always has to choose using the keyboard.

### Solution

The solution is in the file `pbs150-challenge_solution.sh`.

It can be used in several ways, apart from the `-s` flag and `-l` option from last episode.

By default reads from `menu.txt`, so running:

```bash
./pbs150-challenge_solution.sh
```

will duse that file as a source.

By running:

```bash
./pbs150-challenge_solution.sh -m breakfast_menu.txt
```

another file will be used.

Finally, it can be called passing a list of items:

```bash
echo "coffee\ntea\norange juice" | ./pbs150-challenge_solution.sh -m -
```

and it will use those as source.

In all cases, it is possible to redirect the final output to a file:

```bash
echo "coffee\ntea\norange juice" | ./pbs150-challenge_solution.sh -m - > order.txt
```

while, at the same time, using the terminal for interaction with user.

## [Episode 151 of X — Bash: Printf and More](https://pbs.bartificer.net/pbs151)

### Optional Challenge

Write a script to render multiplication tables in a nicely formatted table. Your script should:

1) Require one argument — the number to render the table for
2) Default to multiplying the number given by 1 to 10 inclusive
3) Accept the following two optional arguments:
	* `-m` to specify a minimum value, replacing the default of 1
	* `-M` to specify a maximum value, replacing the default of 10

### Solution

The solution is in the file `pbs151-challenge_solution.sh`.

There are two points of attention. The first one is the management of the ouput. I added a threshold to the maximum number of lines to be displayed before moving to the pager, but this is hard-coded in the script:

```bash
MAX_LINES_NO_PAGER=10
```

At the end of the script, I check for both redirection and number of lines, so that I can pipe to the pager or not:

```bash
# check number of lines in output
num_output_lines=$(printf "%s" "$output_string" | wc -l | xargs)
if [[ $num_output_lines -gt $MAX_LINES_NO_PAGER ]] && [[ -t 1 ]]
then
	# for output longer than limit, pipe to pager
	printf "%s" "$output_string" | less
else
	# for shorter output, or if we are not redirecting, print directly
	printf "%s" "$output_string"
fi
```

Not really satisfactory, will have to look for a better way.

The other point is much more subtle: the script uses the first non-optional arguments as the base number for the multiplication table. It also uses `getopts` to handle the optional arguments. If a negative number is passed, it will be intercepted by `getopts`, since it starts with a `-`, and will be treated as a non-valid option. For now, only positive numbers are accepted as valid inputs.

Another caveat: having my locale set to Italian, I do not have by default the thousands separator. So despite having the script account for them, I don't see them in the result unless I set a different locale when running the script.

Running

```bash
./pbs151-challenge_solution.sh -m -1 -M 5 1000
```

outputs

```Text
1000 x -1 = -1000
1000 x  0 =     0
1000 x  1 =  1000
1000 x  2 =  2000
1000 x  3 =  3000
1000 x  4 =  4000
1000 x  5 =  5000
```

while running

```bash
env LC_ALL=en_US.UTF-8 ./pbs151-challenge_solution.sh -m -1 -M 5 1000
```

outputs

```Text
1,000 x -1 = -1,000
1,000 x  0 =      0
1,000 x  1 =  1,000
1,000 x  2 =  2,000
1,000 x  3 =  3,000
1,000 x  4 =  4,000
1,000 x  5 =  5,000
```

## [Episode 152 of X — Bash: `xargs` & Easier Arithmetic](https://pbs.bartificer.net/pbs152)

### Optional Challenge

Update your solution to the PBS 151 challenge to make use of arithmetic expressions for all your calculations. Also re-evaluate your code to see if there are any places where using `xargs` could simplify your code.

### Solution

The solution is in the file `pbs152-challenge_solution.sh`.

Apart from copying Bart's handling of the pager options, the only improvement is the handling of negative values for the input, left as a drawback in the previous solution. In fact, argumetns starting with a `-` *can* be used, provided that they are preceded by the `--` flag, which signals to `getopts` to stop processing flags. This is actually a standard feature fo many terminal commands.

So changed my solution to also accept negative number in the regex.

So, in the base case running

```bash
./pbs152-challenge_solution.sh 2
```

outputs

```Text
2 x  1 =  2
2 x  2 =  4
2 x  3 =  6
2 x  4 =  8
2 x  5 = 10
2 x  6 = 12
2 x  7 = 14
2 x  8 = 16
2 x  9 = 18
2 x 10 = 20
```

but

```bash
./pbs152-challenge_solution.sh -2
```

results in an error:

```Text
Usage: pbs152-challenge_solution.sh [-s START_VALUE] [-e END_VALUE] -- base_number
```

which can be solved using the `--` flag:

```bash
./pbs152-challenge_solution.sh -- -2
```

```Text
-2 x  1 =  -2
-2 x  2 =  -4
-2 x  3 =  -6
-2 x  4 =  -8
-2 x  5 = -10
-2 x  6 = -12
-2 x  7 = -14
-2 x  8 = -16
-2 x  9 = -18
-2 x 10 = -20
```

and, of course, the `-s` and `-e` optional parameters are retained:

```bash
./pbs152-challenge_solution.sh -s -3 -e 12 -- -2
```

```Text
-2 x -3 =   6
-2 x -2 =   4
-2 x -1 =   2
-2 x  0 =   0
-2 x  1 =  -2
-2 x  2 =  -4
-2 x  3 =  -6
-2 x  4 =  -8
-2 x  5 = -10
-2 x  6 = -12
-2 x  7 = -14
-2 x  8 = -16
-2 x  9 = -18
-2 x 10 = -20
-2 x 11 = -22
-2 x 12 = -24
```

The other main difference with respect to Bart's solution is in how the maximum field length for multiplier and result are determined. Since we are using `seq` to create the array of multipliers, and we are only using integer numbers, it is *guaranteed* that largest number, for both multiplier and result, will be in correspondance to one the minimum or maximum values for the multiplier, since either of them can be negative. Therefore, instead of looping through all the sequence to find the widest fieeld, and then looping once again to calculate the results and create the table, I calculate the field width only for the two extremes, and take the maximum as appropriate.

I also kept the possibility of piping from `STDIN`, just in case.

```bash
echo 2 | ./pbs152-challenge_solution.sh
```

results in

```Text
2 x  1 =  2
2 x  2 =  4
2 x  3 =  6
2 x  4 =  8
2 x  5 = 10
2 x  6 = 12
2 x  7 = 14
2 x  8 = 16
2 x  9 = 18
2 x 10 = 20
```

and  interestingly,

```bash
echo -2 | ./pbs152-challenge_solution.sh
```

works as expected, even without specifying the `--` flag

```Text
-2 x  1 =  -2
-2 x  2 =  -4
-2 x  3 =  -6
-2 x  4 =  -8
-2 x  5 = -10
-2 x  6 = -12
-2 x  7 = -14
-2 x  8 = -16
-2 x  9 = -18
-2 x 10 = -20
```

although it is not clear to me why that is.

## [Episode 153 of X — Bash: `xargs` & Easier Arithmetic](https://pbs.bartificer.net/pbs153)

### Optional Challenge

Finally, if you’d like to put your new-found function writing skills to the test, update your multiplication table script to replace duplicated code blocks with functions.

### Solution

The solution is in the file `pbs153-challenge_solution.sh`.

I created three functions, saved in the file `utility_functions.sh`.

The first function is called `is_integer_number`, and is used to check whether a single input is an integer number (either positive or negative) or not. In this case, only the first input is considered, and all others inputs are discarded. The function can accept either an argument, or a value from `STDIN`, and does not have any flags.

The second function is called `is_number`, and is similar to the first one, except that it accepts non-integer numbers, i.e. with a decimal part, again either positive or negative.

The third function is called `find_max`, and is used to identify the maximum value among all the input arguments, discarding those that are not numbers. Uses `is_number` to detect valid arguments, and accepts a flag to display warnings for any non-valid arguments.

## [Episode 157 of X — Bash: jq: Querying JSON with `jq`](https://pbs.bartificer.net/pbs157)

### Optional Challenge

Can you develop `jq` commands to answer the following questions:

1. What prize did friend of the NosillaCast podcast Dr. Andrea Ghez win? List the year, category, and motivation.
2. How many laureates were there for each prize? List the year, category, and number of winners for each.
3. Which prizes were won outright, i.e. not shared? List the year, category, first name, last name, and motivation for each.

### Solution

For each of the questions, there is a separate `bash` script, with name reflecting the question number:

- `pbs157-challange_solution_01.sh`
- `pbs157-challange_solution_02.sh`
- `pbs157-challange_solution_03.sh`

Adding a short explanation on how the solutions work.

#### Question 1

```bash
jq '.prizes[] |
	select(any(.laureates[]?; .firstname == "Andrea" and .surname == "Ghez")) |
	(.year, .category, (.laureates[]? | select(.firstname == "Andrea" and .surname == "Ghez") | .motivation))' \
	NobelPrizes.json
```

For this, started exploding the original array and piping each prize to a `select` function, where we iterate over the `laureates` array (with the `?` to skip zero-length arrays) and use a second input to the `any` function to select only the ones where the first name is "Andrea" surname is "Ghez". Having identified only the relevant prizes, we extract the information using three selectors. The first two are simple: `.year` and `.category`; for the third one, in order to get to the motivation, we need once again to select only the correct laureate, so we use a subgroup to filter the laureates with the correct first and last name, and extract the motivation from those. I used both first and last name in both filters just to make sure to not select any other "Ghez" that might be present.

The result is:

```Text
"2020"
"physics"
"\"for the discovery of a supermassive compact object at the centre of our galaxy\""
```

#### Question 2

```bash
jq '.prizes[] |
	select(any(.laureates; length > 0)) |
	(.year, .category, (.laureates | length))' \
	NobelPrizes.json
```

In this case, I decided to skip the cases where no prize was awarded, so I added a filter on the number of laureates to be greater than 0. After that it was a simple matter to add the requested outputs; as for the previous question, included a grouping for the third piece of information since it is in a sub-array, and this time it must not be exploded, since we want the length of the array of laureates itself, for each prize. The output is too long to be included here.

#### Queston 3

```bash
jq '.prizes[] |
	select(any(.laureates; length == 1)) |
	(.year, .category, (.laureates[] | (.firstname, .surname, .motivation)))' \
	NobelPrizes.json
```

This is similar to the previous question in the initial filtering, just substituting the condition on the length of the laureates array to have the correct one. For the output, once again need to dig into a sub-array, so a grouping is needed. This time, however, we need information inside each element of the laureates array (even though we know the the array only has a single element), so we can use either `.laureates[]` or `.laureates[0]` with no difference in result. From that element, we extract the remaining information. Again, the output is too long to be included here.

## [Episode 158 of X — More Queries (`jq`)](https://pbs.bartificer.net/pbs158)

### Optional Challenge

Find all the laureates awarded their prize for something to do with quantum physics, i.e. the first name, surname, and motivation for each winner where the motivation contains a word that starts with 'quantum' in any case.

### Solution

The solution is included in a `bash` script, named `pbs158-challange_solution.sh`.

The complete solution is:

```bash
jq '.prizes[] |
	.laureates[]? |
	select(.motivation | test("\\bquantum", "i")) |
	(.firstname, .surname // "--", .motivation)' \
	NobelPrizes.json
```

We start by exploding the original array of prizes, and then exploding each respective laureates array, with a question mark to prevent errors when no prize was awarded. We filter with a `select` function using a `test` with regular expression as criteria. The regular expression is "\\bquantum", with the double "\" for escaping, "\b" indicating a word boundary, and the case-insensitive flag to account for any possible spelling, for example if "Quantum" is the first word after a period. After the filtering, for each laureate matching the criteria we extract the first name, the surname, and the motivation. For the surname, we use the alternate operator to account for the possibility of the prize being awarded to a laureate with no surname, or to an organization. In that case, in place of the surname we output "--" as a marker.

In total, there are 23 laureates for quantum *something*.

## [Episode 159 of X — Building Data Structures (`jq`)](https://pbs.bartificer.net/pbs159)

### Optional Challenge

Using the Nobel Prizes data set as the input, build a simpler data structure to represent just the most important information.

The output data structure should be an array of dictionaries, one for each prize that was actually awarded, indexed by the following fields:

| Field      | Type             | Description                            |
| ---------- | ---------------- | -------------------------------------- |
| year       | Number           | The year the prize was awarded.        |
| prize      | String           | The category the prize was awarded in. |
| numWinners | Number           | The number of winners.                 |
| winners    | Array of Strings | The names of all the winners.          |

Years where there were no prizes awarded should be skipped.

As an example, when pretty printed, the entry for the 1907 peace prize should look like this:

	{
		"year": 1907,
		"prize": "peace",
		"numWinners": 2,
		"winners": [
			"Ernesto Teodoro Moneta",
			"Louis Renault"
		]
	}

The final output should be in JSON format (all on one line not pretty printed) and should be sent to a file named `NobelPrizeList.json`.

#### A Warning and a Tip

The prizes awarded to organisations rather than specific people are likely to trigger a subtle bug in your output — a trailing space at the end of the name due to the fact that the dictionaries representing laureates that are organisations rather than people have no surnames.

To get full credit you should remove this trailing space using the same technique used to remove the leading and trailing quotation marks in one of the examples in this instalment.

A good test for your logic is the 1904 peace prize, the dictionary for which should look like:

	{
		"year": 1904,
		"prize": "peace",
		"numWinners": 1,
		"winners": [
			"Institute of International Law"
		]
	}

**Purely for bonus credit**, you can avoid the need to trim the space from the end of organisational winners by ensuring it never gets added. One way to achieve this is to combine the following jq functions and operators:

1. The alternate operator (`//`)
2. The `empty` function — we’ve not seen it yet, but it takes no arguments and returns absolute nothingness
3. The `join` function

Note that `["Bob", "Dylan"] | join(" ")` results in `"Bob Dylan"`, but `["Bob"] | join(" ")` results in just `"Bob"`.

### Solution

The solution is included in a `bash` script, named `pbs159-challange_solution.sh`.

The complete solution is:

```bash
jq -r '[.prizes[] |
	select(any(.laureates[]?; length > 0)) |
	{
		year: .year | tonumber,
		prize: .category,
		numWinners: .laureates | length,
		winners: [.laureates[] | (["\(.firstname)", ("\(.surname // empty)")] | join(" "))]
	}] | @json' \
	NobelPrizes.json > NobelPrizeList.json
```

First of all, we wrap the entire `jq` command chain in a pair of square brackets `[]`, so that the output will be an array. Inside of that, the first action is to explode the original array of prizes, and then filter by selecting only those having more than 0 laureate (i.e. excluding years when the prize was not assigned). The output of the filter is used to build a new dictionary (one dictionary for each prize), so immediately next we wrap anything else inside a pair of curly brackets `{}`. The elements in the dictionary are:

- the year, that needs to be converted to a number
- the prize, for which we simply take the category of the prize
- the number of winners, for which we calculate the lenght of the `laureates` array
- an array with the full names of laureats

For this last piece of information, we need once again to wrap wint square brackets, since we want an array. Inside of those, we explode the array of laureates (and we don't need the `?` since we already know the `laureates` array exists, since we filtered out those prizes with empty array eralier) and construct the string for the full name. The full name is *usually* obtained by `join`ing the `.firstname` and `.surname` fields for each laureate. But in some case the `.surname` is missing, usually for organizations, but in some case people do not have surnames, for example 1991's Peace Nobel Prize winner Aung San Suu Kyi. In those case, `.surname` will return `null`, so we take advantage of the alternate operator `//` to call the `empty` function. So we either will have a 2-element array (containing first name and surname), or a 1-element array (with only the content of `.firstname`). In either case, we pass the array to the `join` function with a single space as separator.

At the end of everything, i.e. after closing both the curly bracket for the dictionary and the square bracket for the overall array, we pass everything to the formatter `@json` to have `jq` output a `json` file. It is important to call the `jq` command with the `-r` option, otherwise the output will contain additional characters, such as escaped double quotes; that would still be valid `json`, but not as lean as it could be.

The script automatically redirects the output to the file `NobelPrizeList.json`, included in the repository alongside the solution.

To double check the solution, let's extract the 1907's Peace prize, using the command:

```bash
jq '.[] | select(.year == 1907 and .prize == "peace")' NobelPrizeList.json
```

which returns:

```json
{
  "year": 1907,
  "prize": "peace",
  "numWinners": 2,
  "winners": [
    "Ernesto Teodoro Moneta",
    "Louis Renault"
  ]
}
```

as requested, and the 1904's Peace prize, using the command:

```bash
jq '.[] | select(.year == 1904 and .prize == "peace")' NobelPrizeList.json
```

which returns:

```json
{
  "year": 1904,
  "prize": "peace",
  "numWinners": 1,
  "winners": [
    "Institute of International Law"
  ]
}
```

with no extra space at the end of the string for the winner, as requested.

## [Episode 160 of X — jq as a Programming Language (`jq`)](https://pbs.bartificer.net/pbs160)

### Optional Challenge

Develop a more advanced searching script that expects three variables:

1. `search` — a search string to check the laureate names against case-insensitively
2. `minYear` — an earliest year a matching prize can have been awarded in.
3. `maxYear` — a latest year a matching prize can have been awarded in.

For bonus credit, can you make both year arguments effectively optional?

### Solution

The first solution, with all the variables required, is contained in the file `pbs160_challenge_solution_01.jq`, and is:

```jq
# Search the Nobel Prizes data set as published by the Nobel Prize Committee
# by name, and with a range of years
# Input:    JSON as published by the Nobel Committee
# Output:   An array of prize dictionaries
# Variables:
#   $search:    The search string, to search in laureates names (both first name and surname)
#   $minYear    The lower year limit (inclusive) to restrict the results
#   $maxYear    The upper year limit (inclusive) to restrict the results
[
    .prizes[]
    | select((.year | tonumber) >= ($minYear | tonumber))
    | select((.year | tonumber) <= ($maxYear | tonumber))
    | select(any(.laureates[]?;
        "\(.firstname) \(.surname)"
        | ascii_downcase
        | contains($search | ascii_downcase)
    ))
]
```

It is basically the same as the last example in the show notes, but with added two additional `select` filters, to check the prize years, against the minimum and maximum passed as input. The `year` key must be converted to a number because it is encoded as a string in the original `json` file. The variables `$minYear` and `$maxYear` are converted to numbers as well, to allow users to pass them using either `--arg` or `--argjson`, as they prefer.

To test it, since we already know that searching for 'Curie' yields 3 prizes, awarded in 1903, 1911, and 1935, we can call `jq` with this command:

```bash
jq --from-file challenge_solution_01.jq --arg search Curie --argjson minYear 1910 --argjson maxYear 1920 NobelPrizes.json
```

that, as expected, only returns one prize, the one for 1911.

For making the variable optional, we observe that piping to `select(true)` is like not filtering at all, so that all elements are passed through. So we can find a chain of functions to yield `true` when a variable is not passed as input. This can be done by using the `has` function on the `$ARGS.named` object, and negating the result:

```jq
($ARGS.named | has("minYear") | not)
```

This evaluates to `true` when `minYear` is not present in `$ARGS.named`, and to false when it is. In this second case, we can use the `//` operator to use the select criteria in the original solution:

```jq
select(($ARGS.named | has("minYear") | not) // ((.year | tonumber) >= ($ARGS.named.minYear | tonumber)))
```

so that when the variable is not passed, the filter passes everythin, while when the variable is passed, only the prizes matching the criteria are passed on by the filter.

The full solution is therefore:

```jq
# Search the Nobel Prizes data set as published by the Nobel Prize Committee
# by name, and optionally with a minimum and/or maximum year
# Input:    JSON as published by the Nobel Committee
# Output:   An array of prize dictionaries
# Variables:
#   $search:    The search string, to search in laureates names (both first name and surname)
#   $minYear    Optional: the lower year limit (inclusive) to restrict the results
#   $maxYear    Optional: the upper year limit (inclusive) to restrict the results
[
    .prizes[]
    | select(($ARGS.named | has("minYear") | not) // ((.year | tonumber) >= ($ARGS.named.minYear | tonumber)))
    | select(($ARGS.named | has("maxYear") | not) // ((.year | tonumber) <= ($ARGS.named.maxYear | tonumber)))
    | select(any(.laureates[]?;
        "\(.firstname) \(.surname)"
        | ascii_downcase
        | contains($search | ascii_downcase)
    ))
]
```

and is included in file `pbs160_challenge_solution_02.jq`.

We can test by calling

```bash
jq --from-file challenge_solution_02.jq --arg search Curie NobelPrizes.json
```

with no optional variable to yield all 3 prizes;

```bash
jq --from-file challenge_solution_02.jq --arg search Curie --argjson minYear 1910 --argjson maxYear 1920 NobelPrizes.json
```

outputs just 1 of the prizes (1911);

```bash
jq --from-file challenge_solution_02.jq --arg search Curie --argjson minYear 1910 NobelPrizes.json
```

outputs just 2 of the prizes (1911 and 1935); and

```bash
jq --from-file challenge_solution_02.jq --arg search Curie --argjson maxYear 1920 NobelPrizes.json
```

outputs just 2 of the prizes (1903 and 1911)

## [Episode 161 of X — Maths, Assignment & String Manipulation (`jq`)](https://pbs.bartificer.net/pbs161)

### Optional Challenge

Write a jq script that will take as its input the Nobel Prizes data set (`NobelPrizes.json`) and sanitises it using the various assignment operators to achieve the following changes:

1. Add a boolean key named `awarded` to every prize dictionary to indicate whether or not it was awarded.
2. Ensure all prize dictionaries have a `laureates` array. It should be empty for prizes that were not awarded.
3. Add a boolean key named `organisation` to each laureate dictionary, indicating whether or not the laureate is an organisation rather than a person.
4. Add a key named `displayName` to each laureate dictionary. For people, it should contain their first & last names, and for organisations, just the organisation name.

### Solution

The full solution is contained in the file TKTKTK, and is reported here:

```jq
# Sanitises the Nobel Prizes data set as published by the Nobel Prize Committee
# Input:    JSON as published by the Nobel Committee
# Output:   the sanitised JSON, in particular
#   - add a boolean key named `awarded` to every prize dictionary,
#       to indicate whether or not it was awarded.
#   - ensure all prize dictionaries have a `laureates` array.
#       It should be empty for prizes that were not awarded.
#   - add a boolean key named `organisation` to each laureate dictionary,
#       indicating whether or not the laureate is an organisation rather than a person.
#   - add a key named `displayName` to each laureate dictionary. 
#       For people, it should contain their first & last names, 
#       and for organisations, just the organisation name.
#   - convert the year to number

[
    .prizes[]
    # the awarded prizes have a `laureates` key
    | .awarded = has("laureates")
    # for prizes not awarded, the laureates key is not present, so it would
    # return `null` when called. We either reassign it to itself if it is present,
    # or an empty array if it is `null` 
    | .laureates //= []
    # to identify organizations, we can check whether or not the laureate has
    # a surname. However, there are two edge cases, for people who only have
    # a first name, namely "Aung San Suu Kyi" and "Le Duc Tho"
    | .laureates[] |= (.organisation = ((has("surname") | not) 
        and (.firstname != "Aung San Suu Kyi")
        and (.firstname != "Le Duc Tho")))
    # create a new key `displayname` for each laureate, containing
    # both first name and surname (if the latter is present)
    | .laureates[] |= (.displayname = ([.firstname, (.surname // empty)] | join(" ")))
    | .year = (.year | tonumber)
] | @json
```

First of all, the output should again be an array of objects, so we enclose the whole filter in `[]`. Inside those, we start by exploding the array of prizes, in order to process each individual one.

For the `awarded` key, we can check whether the prize doesn't have a `laureates` key. We assing the result of that check to the new `awarded` key, so we use the `=` operator.

For adding the `laureates` key when missing, we can simply update the key overall, using the `|=` operator; in fact, we will use the `//=` operator, so that we can differentiate the value between present and missing. In particular, the assignment we use is `.laureates //= []` so that when it is already present it is passed unchanged, whereas when it is missing it will be assigned an empty array.

For the `organisation` key, we first have to explode the `laureates` array for each prize, and update its value. The naïve method would be to check whether or not the laureate has a `surname` key, sice organization are only recorded with a first name. However, there are two edge cases in the dataset: Aung San Suu Kyi and Le Duc Tho, winners of the Peace prize in 1991 and 1973 respectively, do *not* have a surname (at least in the dataset; this appears to be correct for the former, but not for the latter, whose surname should be Lê). In any case, we have to add the two additional conditions to account for those, and correctly set them as *not* organisations. We need to update the 

For the `displayname`, we can simply reuse the logic of joining an array containing `.firstname` as the first element and `(.surname // empty)` as the second element with a single space. Again, we need to update the value of each item in the `laureates` array, as for the previous key.

While we are at it, we also converted the year to number.

Finally, the whole dataset is converted to JSON format with a `@json` formatter.

The solution can be called with the command:

```bash
jq -r --from-file challenge_solution.jq NobelPrizes.json > NobelPrizes_clean.json
```

The resulting file is includeed.
