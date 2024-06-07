# time_since and time_tense

The `time_since` and `time_tense` filters format time.

## time_since

Formats a human readable time difference from the value to the current time. Eg: **10 minutes ago**

```twig
{{ '2021-03-06 14:25:55'|time_since }}

{{ 'February 14, 2024 14:30'|time_since }}
```

## time_tense

Formats 24-hour time and the day using the grammatical tense of the current time. Eg: Today at 12:49, Yesterday at 4:00 or 18 Sep 2015 at 14:33.

```twig
{{ '2021-03-06 14:25:55'|time_tense }}
```

> **NOTE:** To format dates in Twig there is a [date filter](https://twig.symfony.com/doc/3.x/filters/date.html)
