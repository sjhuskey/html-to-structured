Let's start over. Here is an example of the input I'm working with:

```
<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr>
<th>Γ(άιος) Ἀβίδιος Ἀγαθάνγελος</th>
<th style="text-align: right;">MID II AD</th>
</tr>
</thead>
<tbody>
</tbody>
</table>
<blockquote>
<p>Rizakis #198 (“Antoninus Pius”); IG V 1. 472<sup>a</sup>,
663<sup>b</sup></p>
<p>Lacedaemonian of the Pitanatan obe</p>
<p>Victor in the boys’ wrestling in the Greater Eurykleian games (mid
130s or later) when C. Julius Antipatros (1) was
agonothetes<sup>b</sup></p>
<p>Honored for courage and a piοus life<sup>a</sup></p>
</blockquote>
```

I want to extract the text from the `table` and the `blockquote` and put them into a dictionary, but there is a fair amount of preprocessing that must happen.

There should be two `<th>` tags in the `<table>`. The first `<th>` holds the `name` and the second `<th>` holds the `date`. The `name` should be the text from the first `<th>` and the `date` should be the text from the second `<th>`. If there isn't a `date` value, the placeholder "No date" should be inserted.

The first paragraph holds the values for `Sources`. This is where the preprocessing must occur, since several things must be taken into account.

Sometimes there is more than one source. In that case, the sources are divided by a semicolon and a space ('; ').

Sometimes the sources are subdivided, in which case the subitems are divided by a comma and a space (', '). In these cases, each of the subitems should have a prefix, but only the first item currently has one. We need to identify the prefix and then add it before each subitem.

Furthermore, there are specific patterns for the prefixes that must be observed. They are:

1. `IG ([IVXLCDM]*) ([0-9\.])` (that is, the capital letters "I" and "G" followed by a space, some roman numeral, a space, and an arabic numeral followed by a period)
2. `SEG ([IVXLCDM]*) ([0-9\.])` (that is, the capital letters "S", "E", and "G" followed by a space, some roman numeral, a space, and an arabic numeral followed by a period)

In all other cases, just handle the alphabetical prefixes (e.g., "Poralla", "Rizakis") as they are.

Whenever there is one or more lower-case letters in `<sup>` at the end of a source, they must be added as values to the "Reference" key.

The HTML example above should end up looking like this:

```
{
    "ID": "f0eefdb00efd941c51e645d1668611d9",
    "Name": "Γ(άιος) Ἀβίδιος Ἀγαθάνγελος",
    "Date": "MID II AD",
    "Sources": [
        {
            "Resource": "Rizakis #198 (“Antoninus Pius”)"
        },
        {
            "Resource": "IG V 1. 472",
            "Reference": "a"
        },
        {
            "Resource": "IG V 1. 663",
            "Reference": "b"
        }
    ],
    "Facts_HTML": [
        "<p>Lacedaemonian of the Pitanatan obe</p>",
        "<p>Victor in the boys’ wrestling in the Greater Eurykleian games (mid\n130s or later) when C. Julius Antipatros (1) was\nagonothetes<sup>b</sup></p>",
        "<p>Honored for courage and a piοus life<sup>a</sup></p>"
    ]
}
```