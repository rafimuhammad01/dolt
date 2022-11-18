# ISP Documentation for ParseDate

Function selected : 
`ParseDate (dateStr string) (time.Time, error)` 
```go
var SupportedLayouts = []string{
	"2006/01/02",
	"2006/01/02T15:04:05",
	"2006/01/02T15:04:05Z07:00",

	"2006.01.02",
	"2006.01.02T15:04:05",
	"2006.01.02T15:04:05Z07:00",

	"2006-01-02",
	"2006-01-02T15:04:05",
	"2006-01-02T15:04:05Z07:00",
}

// Parses a date string. Used by multiple commands.
func ParseDate(dateStr string) (time.Time, error) {
	for _, layout := range SupportedLayouts {
		t, err := time.Parse(layout, dateStr)

		if err == nil {
			return t, nil
		}
	}

	return time.Time{}, errors.New("error: '" + dateStr + "' is not in a supported format.")
}
```

## Characteristic
Whether if dateStr format supported

## Blocks
- "2006/01/02"
- "2006/01/02T15:04:05"
- "2006/01/02T15:04:05Z07:00" 
- "2006.01.02"
- "2006.01.02T15:04:05"
- "2006.01.02T15:04:05Z07:00"
- "2006-01-02"
- "2006-01-02T15:04:05"
- "2006-01-02T15:04:05Z07:00"
- invalid

These blocks are about the format not the value. This value is the known value by golang to convert string with its format to golang time.Time

for example:
if I have string with value "2022/02/02" and I want to convert it to golang time.Time then the code will be

```go
time.Parse("2006/01/02", "2022/02/02")
```

## Input Domain Model
| characteristic    | b1           | b2 | b3| b4| b5 | b6 | b7 | b8 | b9 | b10
|-------------------|--------------|---|---| ---| --- | --- | ---| --- | --- | --- |
|q1 = dateStr Format| "2006/01/02" | "2006/01/02T15:04:05" |"2006/01/02T15:04:05Z07:00" | "2006.01.02"| "2006.01.02T15:04:05" | "2006.01.02T15:04:05Z07:00"| "2006-01-02"| "2006-01-02T15:04:05" | "2006-01-02T15:04:05Z07:00"| invalid |

## IDM Relabeling Model 
| characteristic    | b1           | b2 | b3| b4| b5 | b6 | b7 | b8 | b9 | b10
|-------------------|--------------|---|---| ---| --- | --- | ---| --- | --- | --- |
|A| A1 | A2 | A3 | A4 |A5 | A6 | A7 | A8 | A9 | A10 |

Maximum of test is 10

## Constraint between characteristics
All the characteristics is already disjoint that means they are all have no intersection between each other

## Test Values and Example I/O

### Possible Test Value
|Test Value| Example Input | Example Output|
| --- | --- | --- |
|A1| "2022/11/18" | time.Date(2022, 11, 18, 0, 0, 0, 0, time.UTC), false
|A2| "2022/11/18T21:00:00" | time.Date(2022, 11, 18, 21, 0, 0, 0, time.UTC), false
|A3| "2022/11/18T21:00:05Z07:00"  | time.Date(2022, 11, 18, 0, 0, 0, 0, time.LoadLocation("Asia/Jakarta)), false
|A4| "2022.11.18" |time.Date(2022, 11, 18, 0, 0, 0, 0, time.UTC), false
|A5| "2022.11.18T21:00:00" |  time.Date(2022, 11, 18, 21, 0, 0, 0, time.UTC), false
|A6| "2022.11.18T21:00:05Z07:00" | time.Date(2022, 11, 18, 0, 0, 0, 0, time.LoadLocation("Asia/Jakarta)), false
|A7| "2022-11-18" | time.Date(2022, 11, 18, 0, 0, 0, 0, time.UTC), false
|A8| "2022-11-18T21:00:00" | time.Date(2022, 11, 18, 21, 0, 0, 0, time.UTC), false
|A9| "2022-11-18T21:00:05Z07:00" | time.Date(2022, 11, 18, 0, 0, 0, 0, time.LoadLocation("Asia/Jakarta)), false
|A10|  "20221118" |  nil, true


### All Combinations Criterion 
Since this function only have 1 parameter, there is no combination between the parameter. Thus, it will have 9 test for all the supported format and also 1 test for the unsupported format

| ACoC |
| --- |
|A1|
|A2| 
|A3|
|A4| 
|A5|
|A6| 
|A7|
|A8| 
|A9|
|A10| 

## Selected Test Value
Since the ACoC doesn't have any combination we will use all the possible test value for creating test, so the selected test value will be the same as the possible test value that already mentioned before, that is:

|Test Value| Input |
| --- | --- |
|A1 | "2022/11/18" |
|A2 | "2022/11/18T21:00:00" |
|A3 | "2022/11/18T21:00:05Z07:00"  |
|A4 | "2022.11.18" |
|A5 | "2022.11.18T21:00:00" |
|A6 | "2022.11.18T21:00:05Z07:00" |
|A7 | "2022-11-18" |
|A8 | "2022-11-18T21:00:00" |
|A9 | "2022-11-18T21:00:05Z07:00" |
|A10 |  "20221118" |

## Existing Test 

This is the existing test and as we can see almost all the possible format covered but there is 3 format that not yet tested which is:
- "2006/01/02T15:04:05Z07:00"
- "2006.01.02T15:04:05Z07:00"
- "2006-01-02T15:04:05Z07:00"

```go
{
    // format of test case 
    // {input, output, isError}
    {"1901/09/30", time.Date(1901, 9, 30, 0, 0, 0, 0, time.UTC), false},
    {"2019/01/20", time.Date(2019, 1, 20, 0, 0, 0, 0, time.UTC), false},
    {"2019-1-20", time.Date(2019, 1, 20, 0, 0, 0, 0, time.UTC), true},
    {"2019.01.20", time.Date(2019, 1, 20, 0, 0, 0, 0, time.UTC), false},
    {"2019/01/20T13:49:59", time.Date(2019, 1, 20, 13, 49, 59, 0, time.UTC), false},
    {"2019-01-20T13:49:59", time.Date(2019, 1, 20, 13, 49, 59, 0, time.UTC), false},
    {"2019.01.20T13:49:59", time.Date(2019, 1, 20, 13, 49, 59, 0, time.UTC), false},
    {"2019.01.20T13:49", time.Date(2019, 1, 20, 13, 49, 59, 0, time.UTC), true},
    {"2019.01.20T13", time.Date(2019, 1, 20, 13, 49, 59, 0, time.UTC), true},
    {"2019.01", time.Date(2019, 1, 20, 13, 49, 59, 0, time.UTC), true},
}
```


