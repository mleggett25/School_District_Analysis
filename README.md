# School District Analysis with Pandas

## Overview of School District Analysis

### Purpose
The purpose of this school district analysis was to provide insight into the performance trends of reading and math standardized test data across 15 high schools. After completing the analysis, however, the school board found evidence of academic dishonesty in the reading and math grades for Thomas High School ninth graders. The analysis was run again, but this time, the math and reading scores for Thomas High School ninth graders were replaced with NaNs.

## Results

### District Summary
To produce the original Disctrict Summary, I created various series to form the columns and then combined them into a data frame.
 
 ```
school_count = len(school_data_complete_df["school_name"].unique())
student_count = school_data_complete_df["Student ID"].count()
total_budget = school_data_df["budget"].sum()
average_reading_score = school_data_complete_df["reading_score"].mean()
average_math_score = school_data_complete_df["math_score"].mean()
average_reading_score = school_data_complete_df["reading_score"].mean()
passing_math_count = passing_math["student_name"].count()
passing_math_count = school_data_complete_df[(school_data_complete_df["math_score"] >= 70)].count()["student_name"]
passing_reading_count = passing_reading["student_name"].count()
passing_reading_count = school_data_complete_df[(school_data_complete_df["reading_score"] >= 70)].count()["student_name"]
passing_math_reading = school_data_complete_df[(school_data_complete_df["math_score"] >= 70) & (school_data_complete_df["reading_score"] >= 70)]
overall_passing_math_reading_count = passing_math_reading["student_name"].count()
overall_passing_percentage = overall_passing_math_reading_count / student_count * 100

district_summary_df = pd.DataFrame(
          [{"Total Schools": school_count,
          "Total Students": student_count,
          "Total Budget": total_budget,
          "Average Math Score": average_math_score,
          "Average Reading Score": average_reading_score,
          "% Passing Math": passing_math_percentage,
         "% Passing Reading": passing_reading_percentage,
        "% Overall Passing": overall_passing_percentage}])
```

I then formatted the columns so that the Average Math Score and Average Reading Score rounded to one decimal place, and the % Passing Math, % Passing Reading, and % Overall Passing columns had no decimals.

```
district_summary_df["Average Math Score"] = district_summary_df["Average Math Score"].map("{:.1f}".format)
district_summary_df["Average Reading Score"] = district_summary_df["Average Reading Score"].map("{:.1f}".format)
district_summary_df["% Passing Math"] = district_summary_df["% Passing Math"].map("{:.0f}".format)
district_summary_df["% Passing Reading"] = district_summary_df["% Passing Reading"].map("{:.0f}".format)
district_summary_df["% Overall Passing"] = district_summary_df["% Overall Passing"].map("{:.0f}".format)

district_summary_df
```

The output from running the code produces the following data frame:

!

I then had to replace the math and reading scores for Thomas High School ninth graders with NaNs, which required that I create a new District Summary.

```
student_data_df.loc[(student_data_df["grade"] == "9th") & (student_data_df["school_name"] == "Thomas High School"), "reading_score"] = np.nan
student_data_df.loc[(student_data_df["grade"] == "9th") & (student_data_df["school_name"] == "Thomas High School"), "math_score"] = np.nan
```

I calculated the number of students that are Thomas High School ninth graders with NaNs and subtracted that number from the student count to create a new student count.

```
student_no_grades = len(student_data_df.loc[(student_data_df["grade"] == "9th") & (student_data_df["school_name"] == "Thomas High School")])
new_student_count = student_count - student_no_grades
```

I then recalculated the series in the data frame with the new total student count and produced the following data frame:

!

As we can see from the tables, the district summary was not significantly affected with the removal of the Thomas High School ninth graders. If we take the unrounded math passing percentage, for example, the difference between the original and new District Summary is by a couple decimal points: 74.76 for the original and 74.98 for the new District Summary. Since we only removed a small portion of the total student population (461 Thomas High School ninth graders out of 39170 total students), we can see why there would be no significant change between the original and new District Summaries.

### School Summary
To produce the original School Summary, I created various series to form the columns and then combined them into a data frame.

```
per_school_types = school_data_df.set_index(["school_name"])["type"]
per_school_counts = school_data_complete_df["school_name"].value_counts()
per_school_budget = school_data_complete_df.groupby(["school_name"]).mean()["budget"]
per_school_capita = per_school_budget / per_school_counts
per_school_math = school_data_complete_df.groupby(["school_name"]).mean()["math_score"]
per_school_reading = school_data_complete_df.groupby(["school_name"]).mean()["reading_score"]
per_school_passing_math = school_data_complete_df[(school_data_complete_df["math_score"] >= 70)]
per_school_passing_reading = school_data_complete_df[(school_data_complete_df["reading_score"] >= 70)]
per_school_passing_math = per_school_passing_math.groupby(["school_name"]).count()["student_name"]
per_school_passing_reading = per_school_passing_reading.groupby(["school_name"]).count()["student_name"]
per_school_passing_math = per_school_passing_math / per_school_counts * 100
per_school_passing_reading = per_school_passing_reading / per_school_counts * 100
per_passing_math_reading = school_data_complete_df[(school_data_complete_df["reading_score"] >= 70)
                                               & (school_data_complete_df["math_score"] >= 70)]
per_passing_math_reading = per_passing_math_reading.groupby(["school_name"]).count()["student_name"]
per_overall_passing_percentage = per_passing_math_reading / per_school_counts * 100

per_school_summary_df = pd.DataFrame({
    "School Type": per_school_types,
    "Total Students": per_school_counts,
    "Total School Budget": per_school_budget,
    "Per Student Budget": per_school_capita,
    "Average Math Score": per_school_math,
    "Average Reading Score": per_school_reading,
    "% Passing Math": per_school_passing_math,
    "% Passing Reading": per_school_passing_reading,
    "% Overall Passing": per_overall_passing_percentage})

per_school_summary_df.head()
```

I then formatted the Total School Budget and the Per Student Budget columns to include the dollar sign ($) and round to two decimal places.

```
per_school_summary_df["Total School Budget"] = per_school_summary_df["Total School Budget"].map("${:,.2f}".format)
per_school_summary_df["Per Student Budget"] = per_school_summary_df["Per Student Budget"].map("${:,.2f}".format)
```

The output from running the code produces the following data frame:

!

With the grades of the Thomas High School ninth graders already replaced with NaNs, I calculated the total number of tenth to twelfth graders at Thomas High School.

```
tenth_to_twelfth_THS = school_data_complete_df[(school_data_complete_df["school_name"] == "Thomas High School") & (school_data_complete_df["grade"] != "9th")]
```

I then recalculated the series in the data frame with the tenth to twelfth graders count and produced the following data frame:

!

As we can see from the tables, there is a significant difference between the passing math percentage, the passing reading percentage, and the overall passing percentage for Thomas High School between the original and new School Summaries, with about a 40% increase in all the percentages. Relative to the performance of the other 14 schools, Thomas High School jumps from the eighth highest overall passing percentage to the second highest overall passing percentage, though only marginally compared to four other schools who also had a 90% overall passing percentage.

### Affects By Grade
To show the affects by grade, 
