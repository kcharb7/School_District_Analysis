# PyCity Schools with Pandas
## Overview
### *Purpose* 
Maria, a Chief Scientist for a city school district, has asked for my help in analyzing data on student funding and students’ standardized test scores on math and reading. Analysis of trends are to be presented to the school board so they can make decisions regarding school budgets and priorities. The list of deliverables included:
-	A high-level snapshot of the district's key metrics, presented in a table format
-	An overview of the key metrics for each school, presented in a table format
-	Tables presenting each of the following metrics:
  - Top 5 and bottom 5 performing schools, based on the overall passing rate
  - The average math score received by students in each grade level at each school
  - The average reading score received by students in each grade level at each school
  - School performance based on the budget per student
  - School performance based on the school size 
  - School performance based on the type of school

## Resources
-	Data Source: Pstudents_complete.csv, schools_complete.csv, missing_grades.csv, clean_students_complete.csv
-	Software: Python 3.6.1, Jupyter Notebook 6.2.0

## Preparing the Data
### *Add the Pandas Dependency*
To begin my analysis, I imported the Pandas Dependency with the following code:
```
import pandas as pd
```

### *Read the CSV Files*
Next, I imported my csv files to examine their contents, first opening and reading the schools_complete.csv file:
```
# Read the school data file and store it in a Pandas DataFrame.
school_data_df = pd.read_csv(school_data_to_load)
school_data_df
```
My output looked as follows:

![School_data](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/School_data.png)

Then, I used the above code to open and read the students_complete.csv file, using the df.head() function to only look at the first 5 rows as this dataset was much larger:
```
# Read the student data file and store it in a Pandas DataFrame.
student_data_df = pd.read_csv(student_data_to_load)
student_data_df.head()
```
My output looked as follows:

![student_data](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Student_data.png)

### *Cleaning the Data*
To clean the data, I first determined if there were any missing values in the rows of the CSV files. Using the isnull() method I ran the following code to determine the number of empty within the school data: 
```
# Determine if there are any missing values in the school data.
school_data_df.isnull()
```
The output indicated that there was no missing data. 
![School_isnull](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/School_isnull.png)

Using this same method in addition to the sum() method, I executed code to determine the number of empty rows within the students_complete.csv:
```
# Determine if there are any missing values in the student data.
student_data_df.isnull().sum()
``` 
The output indicated there was no missing data. 

![Student_isnull](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Student_isnull.png)

### *Determine Data Types*
To determine the data types in the school_data DataFrame, I used the dtypes attribute:
```
# Determine data types for the school DataFrame.
school_data_df.dtypes
```
The output looked as follows:

![School_dtype](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/School_dtype.png)

I modified the above code to determine the date types in the students_data DataFrame:
```
# Determine data types for the student DataFrame.
student_data_df.dtypes
```
The output looked as follows:

![Student_dtype](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Student_dtype.png)

This output confirmed that all columns needed for calculations were integers. 

## Incorrect Student Names
When first examining the data, I found that one of the student’s names had the prefix “Dr.” and thus wanted to further investigate the data to find all the students’ names with a professional prefix or suffix. I began by creating a new file to test my new code in. Then I gathered the students’ names in a separate file using the tolist() method so that the DataFrame was not affected: 
```
# Put the student names in a list.
student_names = student_data_df["student_name"].tolist()
student_names
```
Then, I used a for loop to iterate through the student_names list. Within the for loop I included the split() method and counted the length of each name to determine which students had a prefix or suffix (i.e., a length of 3):
```
# Split the student name and determine the length of the split name.
for name in student_names:
    print(name.split(), len(name.split()))
```
To find the names with lengths of 3 or higher, I created a conditional statement that produced a new list of names if the length of the name was equal to or greater than 3:
```
# Create a new list and use it for the for loop to iterate through the list.
students_to_fix = []

# Use an if statement to check the length of the name.
# If the name is greater than or equal to "3", add the name to the list.
for name in student_names:
    if len(name.split())>=3:
        students_to_fix.append(name)
# Get the length of the students whose names are greater than or equal to "3".
len (students_to_fix)
```
The output indicated 1,531 students had names with lengths equal to or greater than 3.

To get all the prefixes, I wrote an algorithm to iterate through the student_to_fix list and determine the length of the prefix/first item in the list is less than or equal to 4 and add it to a new prefixes list. 4 was set as the upper limit as the longest prefix found was “Miss”:
```
# Add the prefixes less than or equal to 4 to a new list.
prefixes = []
for name in students_to_fix:
    if len(name.split()[0])<=4:
        prefixes.append(name.split()[0])
print(prefixes)
```
Similarly, I wrote an algorithm to iterate through the student_to_fix list and determine the length of the suffix/last item in the list is less than or equal to 3 and add it to a new suffixes list. 3 was selected as the longest suffixes found were 3 characters long:
```
# Add the suffixes less than or equal to 3 to a new list:
suffixes = []
for name in students_to_fix:
    if len(name.split()[-1])<=3:
        suffixes.append(name.split()[-1])
print(suffixes)
```
Next, I used the set() method to better identify the prefixes in the prefixes list:
```
# Get the unique items in the "prefixes" list.
set(prefixes)
```
The output identified the prefixes Dr., Mr., Mrs., Ms., and Miss. 

The same method was applied to the suffixes list:
```
# Get the unique items in the "suffixes" list.
set(suffixes)
```
The output indicated the suffixes MD, DDS, DVM, Ph.D., Jr., II, III, IV, and V. The suffixes Jr., II, III, IV, and V are family-related suffixes and were not removed. 

I declared a list named prefixes_suffixes to hold all the common prefixes and suffixes as strings:
```
# Add each prefix and suffix to remove to a list.
prefixes_suffixes = ["Dr. ", "Mr. ","Ms. ", "Mrs. ", "Miss ", " MD", " DDS", " DVM", " PhD"]
```
I created a for loop to loop through this prefixes_suffixes list. Within the for loop, I used the replace() method to replace all the prefixes and suffixes with an empty string. As the data type for student_name was an object, I added the str attribute beside the replace() to convert the object to a string:
```
# Iterate through the "prefixes_suffixes" list and replace them with an empty space, "", when it appears in the student's name.
for word in prefixes_suffixes:
    student_data_df["student_name"] = student_data_df["student_name"].str.replace(word,"")
```
To confirm that all of the prefixes and suffixes were caught in my script, I declared a new variable and assigned it to the student_data_df[“student_name”] and converted the student_data_df[“student_name”] to a list:
```
# Put the cleaned students' names in another list.
student_names = student_data_df["student_name"].tolist()
student_names
```
Then, I created code to add all students’ names that are greater than or equal to 3 to a list:
```
# Create a new list and use it for the for loop to iterate through the list. 
students_fixed = []
#Use an if statement to check the length of the name.
# If the name is greater than or equal to 3, add the name to the list.
for name in student_names:
    if len(name.split())>=3:
        students_fixed.append(name)
#Get the length of the student's names that are greater than or equal to 3.
len(students_fixed)
```
The output indicated that 151 students were in the students_fixed list. Printing this list showed that the only names included in this list were those with familial suffixes, confirming that all professional prefixes and suffixes were caught and removed. 

Knowing that my code worked, I returned to my PyCitySchools.ipynb file to apply the same method in the student_data_df DataFrame. I copied over the code that created a list of prefixes and suffixes:
```
# Add each prefix and suffix to remove to a list.
prefixes_suffixes = ["Dr. ", "Mr. ","Ms. ", "Mrs. ", "Miss ", " MD", " DDS", " DVM", " PhD"]
```
Then, I included the code to replace all the prefixes and suffixes:
```
# Iterate through the words in the "prefixes_suffixes" list and replace them with an empty space, "", when it appears in the student's name.
for word in prefixes_suffixes:
    student_data_df["student_name"] = student_data_df["student_name"].str.replace(word,"")
```

## School District Summary
The school district summary was to be a high-level snapshot of the district’s key metrics, including:
-	Total number of students
-	Total number of schools
-	Total budget
-	Average math score
-	Average reading score
-	Percentage of students who passed math
-	Percentage of students who passed reading
-	Overall passing percentage
To perform these analyses, I merged the school_data_df and student_data_df on their shared column school_name using the merge() method:
```
# Combine the data into a single dataset.
school_data_complete_df = pd.merge(student_data_df, school_data_df, on = ["school_name", "school_name"])
school_data_complete_df.head()
```

### *Number of Students*
To get the total number of students in the school district, I added the count() method to the school_data_complete_df DataFrame and selected the column that identifies with students, “Student ID”:
```
# Get the total number of students.
student_count = school_data_complete_df["Student ID"].count()
student_count
```
The number of students in the district was 39,170.

### *Number of Schools*
To determine the number of schools within the school_data_complete_df DataFrame I used the unique method to gather the unique items in the “school_name” column and then determined the length of the list produced:
```
# Calculate the total number of schools.
school_count_2 = school_data_complete_df["school_name"].unique()
school_count = len(school_count_2)
```
There were 15 different schools.

### *Total Budget*
To determine the total budget for the whole district, I used the sum() method on the “budget” column within the school_data_df DataFrame:
```
# Calculate the total budget.
total_budget = school_data_df["budget"].sum()
total_budget
```
The total budget for the whole district was $24,649,428. 

### *Average Math and Reading Scores*
Using the mean() method and the school_data_complete_df DataFrame, I calculated the average reading scores:
```
# Calculate the average reading score.
average_reading_score = school_data_complete_df["reading_score"].mean()
average_reading_score
```
The average reading score was 81.87784018381414.
Using the same method as above, I calculated the average math score:
```
# Calculate the average math score.
average_math_score = school_data_complete_df["math_score"].mean()
average_math_score
```
The average math score was 78.98537145774827. 

### *Passing Percentages*
In this school district, the passing score for both the reading and math assessment tests was 70. In a new cell, I assigned two variables, passing_math and passing_reading, to the math_score and reading_score columns within the school_data_complete_df DataFrame where all scores were greater or equal to 70:
```
passing_math = school_data_complete_df["math_score"]>=70
passing_reading = school_data_complete_df["reading_score"]>=70
```
To get the number of students who passed, I filtered the school_data_complete_df by adding the school_data_complete_df["math_score"]>=70 within brackets:
```
# Get all the students who are passing math in a new DataFrame.
passing_math = school_data_complete_df[school_data_complete_df["math_score"]>=70]
passing_math.head()
```
The same was done with the reading_score:
```
# Get all the students who are passing reading in a new DataFrame.
passing_reading = school_data_complete_df[school_data_complete_df["reading_score"]>=70]
passing_reading.head()
```
To get the number of students who passed math and reading, I applied the count() method to the student_name column of the passing_math and passing_reading columns:
```
# Calculate the number of students passing math.
passing_math_count = passing_math["student_name"].count()
print(passing_math_count)

#Calculate the number of students passing reading.
passing_reading_count = passing_reading["student_name"].count()
print(passing_reading_count)
```
The output indicated that 29,370 students passed math and 33,610 students passed reading.
After obtaining the count for the number of students who passed math and reading, I calculated the percentage by dividing the math and reading counts by the total number of students and multiplying by 100. For this calculation to work, I also converted the student_count to a floating-point decimal by using float():
```
# Calculate the percentage that passed math.
passing_math_percentage = passing_math_count/float(student_count)*100
print(passing_math_percentage)

#Calculate the percentage that passed reading.
passing_reading_percentage = passing_reading_count/float(student_count)*100
print(passing_reading_percentage)
```
74.98% of students passed math and 85.81% of students passed reading.
To calculate the overall passing percentage, I filtered the school_data_complete_df DataFrame by adding the school_data_complete_df["math_score"]>=70 and school_data_complete_df["reading_score"]>=70 using “&” within brackets:
```
# Calculate the students who passed both math and reading.
passing_math_reading = school_data_complete_df[(school_data_complete_df["math_score"] >= 70) & (school_data_complete_df["reading_score"] >= 70)]

passing_math_reading.head()
```
Then, I applied the count() method to the passing_math_reading DataFrame to get the total number of students who passed both math and reading:
```
# Calculate the number of students who passed both math and reading.
overall_passing_math_reading_count = passing_math_reading["student_name"].count()
overall_passing_math_reading_count
```
The total number of students who passed both math and reading was 25,528.
Dividing the overall_passing_math_reading_count by the toal number of students and multiplying by 100, I calculated the percentage of students who passed both math and reading:
```
# Calculate the overall passing percentage.
overall_passing_percentage = overall_passing_math_reading_count / student_count * 100
overall_passing_percentage
```
65.17% of students passed both reading and math.

### *Create a District Summary DataFrame*
After all the calculations for the district summary were complete, I created a new DataFrame named district_summary_df by creating a list of dictionaries that included all the calculations:
```
# Adding a list of values with keys to create a new DataFrame.
district_summary_df = pd.DataFrame(
          [{"Total Schools": school_count,
          "Total Students": student_count,
          "Total Budget": total_budget,
          "Average Math Score": average_math_score,
          "Average Reading Score": average_reading_score,
          "% Passing Math": passing_math_percentage,
         "% Passing Reading": passing_reading_percentage,
        "% Overall Passing": overall_passing_percentage}])
district_summary_df
```

### *Format Colums*
To clean up the DataFrame, I set to format the budget to two decimal places, the grade averages to one decimal place, and the grade percentages to the nearest whole number percent, as well as add a thousand separator for numbers greater than 1,000.
Starting with the “Total Students” column, I used the map() and format() functions to format the Total Students column with a thousands separator:
```
# Format the "Total Students" to have the comma for a thousands separator.
district_summary_df["Total Students"] = district_summary_df["Total Students"].map("{:,}".format)

district_summary_df["Total Students"]
```
Using the same method, I formatted the “Total Budget” column with a US dollar sign, a thousands separator, and to two decimal places:
```
# Format "Total Budget" to have the comma for a thousands separator, a decimal separator, and a "$".

district_summary_df["Total Budget"] = district_summary_df["Total Budget"].map("${:,.2f}".format)

district_summary_df["Total Budget"]
```
Continuing with this method, I formatted the “Average Reading Score” and “Average Math Score” columns to one decimal place, and the “% Passing Reading”, “% Passing Math”, and “% Overall Passing” columns to the nearest whole number percentage:
```
# Format the columns.
district_summary_df["Average Math Score"] = district_summary_df["Average Math Score"].map("{:.1f}".format)

district_summary_df["Average Reading Score"] = district_summary_df["Average Reading Score"].map("{:.1f}".format)

district_summary_df["% Passing Math"] = district_summary_df["% Passing Math"].map("{:.0f}".format)

district_summary_df["% Passing Reading"] = district_summary_df["% Passing Reading"].map("{:.0f}".format)

district_summary_df["% Overall Passing"] = district_summary_df["% Overall Passing"].map("{:.0f}".format)
```
Running the code district_summary_df, the output looked as follows:

![District_Summary](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/District_Summary.png)

## School Summaries
Following completion of the district summary, my next task was to generate a similar summary for each school in the district. 
### *Create New DataFrame*
I began by creating a new DataFrame so that the index is the school_name and “School Type” is the first column. I used data from the school_data_df DataFrame to create my new DataFrame:
```
# Determine the school type.
per_school_types = school_data_df.set_index(["school_name"])["type"]
per_school_types
```
I took the series produced from the code above and converted it to a new DataFrame:
```
# Add the per_school_types into a DataFrame for testing.
df = pd.DataFrame(per_school_types)
df
```

### *Student Count Per School*
Next to the “type” of school column, I set out to add the total number of students in each school. Two DataFrames, the school_data_df and school_data_complete_df, contained the total number of students per school. I began by getting the student count in the school_data_df DataFrame:
```
# Calculate the total student count.
per_school_counts = school_data_df["size"]
per_school_counts
```
The output indicated that this Series did not have an index with “school_name”. To fix this, I used the set_index() method on the “school_name” column and selected the “size” column to display the student count:
```
# Calculate the total student count.
per_school_counts = school_data_df.set_index(["school_name"])["size"]
per_school_counts
```
To get the student count from the school_data_complete_df DataFrame, I used the value_counts() methods on the “school_name” column to count the number of times a high school appears:
```
# Calculate the total student count.
per_school_counts = school_data_complete_df["school_name"].value_counts()
per_school_counts
```

### *Budget per Student*
To get the budget per student I used the set_index() method on the “school_name” column of the school_data_df DataFrame:
```
# Calculate the total school budget.
per_school_budget = school_data_df.set_index(["school_name"])["budget"]
per_school_budget
```
To get the budget per student, I divided the per_school_budget by the per_school_counts:
```
# Calculate the per capita spending.
per_school_capita = per_school_budget / per_school_counts
per_school_capita
```

### *Score Averages Per School*
Next, I determined the average math and reading scores for each school by applying the mean() method to the groupby() function within the school_data_complete_df DataFrame:
```
# Calculate the average test scores.
per_school_math = school_data_complete_df.groupby(["school_name"]).mean()["math_score"]
per_school_math

per_school_reading = school_data_complete_df.groupby(["school_name"]).mean()["reading_score"]
per_school_reading
```

### *Passing Percentages Per School*
To calculate the number of students who passed math or reading for each school, I modified the code that was used to calculate the number of students who passed math and reading for the district summary by adding the groupby() function and the count() method:
```
# Calculate the passing scores by creating a filtered DataFrame.
per_school_passing_math = school_data_complete_df[(school_data_complete_df["math_score"] >= 70)]
per_school_passing_reading = school_data_complete_df[(school_data_complete_df["reading_score"] >= 70)]

# Calculate the number of students passing math and passing reading by school.
per_school_passing_math = per_school_passing_math.groupby(["school_name"]).count()["student_name"]
per_school_passing_reading = per_school_passing_reading.groupby(["school_name"]).count()["student_name"]
```
To determine the percentage of students who passed math and reading for each school, I divided the per_school_passing_math and per_school_passing_reading values by per_school_counts and then multiplied by 100:
```
# Calculate the percentage of passing math and reading scores per school.
per_school_passing_math = per_school_passing_math / per_school_counts * 100

per_school_passing_reading = per_school_passing_reading / per_school_counts * 100
```
To calculate the overall passing percentage for both math and reading I first calculated the number of students who passed both math and reading for each school:
```
# Calculate the students who passed both math and reading.
per_passing_math_reading = school_data_complete_df[(school_data_complete_df["math_score"] >= 70) & (school_data_complete_df["reading_score"] >= 70)]
per_passing_math_reading.head()
```
Then, I set the index as the school_name using the grouby() function and used the count() method for the student_name to get the total number of students who passed both reading and math per school:
```
# Calculate the number of students who passed both math and reading.
per_passing_math_reading = per_passing_math_reading.groupby(["school_name"]).count()["student_name"]
```
The per_passing_math_reading value was divided by the per_school_counts and then multiplied by 100 to get the overall percentage:
```
# Calculate the overall passing percentage.
per_overall_passing_percentage = per_passing_math_reading / per_school_counts * 100
```

### *Create the School Summary DataFrame*
I created a new DataFrame named per_school_summary_df by creating a list of dictionaries using the column names as the keys and the data obtained above as the values:
```
# Adding a list of values with keys to create a new DataFrame.
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

### *Format the DataFrame*
Following creation of the new DataFrame, I formatted the “Total School Budget” and “Per Student Budget” columns to include a US dollar sign, two decimal places, and a thousands separator using the map() function and applying formatting within the parentheses:
```
# Format the Total School Budget and the Per Student Budget columns.
per_school_summary_df["Total School Budget"] = per_school_summary_df["Total School Budget"].map("${:,.2f}".format)
per_school_summary_df["Per Student Budget"] = per_school_summary_df["Per Student Budget"].map("${:,.2f}".format)
# Display the data frame
per_school_summary_df.head()
```
![Per_School_Summary](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Per_School_Summary.png)

## High and Low Performing Schools
### *Highest-Performing Schools*
The highest-performing schools were asked to be determined based on the overall percentages of passing students. Thus, I sorted the “% Overall Passing” column from highest to lowest within the per_school_summary_df DataFrame:
```
# Sort and show top five schools.
top_schools = per_school_summary_df.sort_values(["% Overall Passing"], ascending=False)
top_schools.head()
```
![Top_Schools](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Top_Schools.png)

The top five highest-performing schools included Cabrera High School, Thomas High School, Griffin High School, Wilson High School, and Pena High School, all of which were charter schools and had student counts below 2,300. 

### *Lowest-Performing Schools*
To determine the five lowest-performing schools based on the overall percentage passing, I used the same code as was used to determine the highest-performing schools but sorted them in ascending order:
```
# Sort and show top five schools.
bottom_schools = per_school_summary_df.sort_values(["% Overall Passing"], ascending=True)
bottom_schools.head()
```
![Bottom_Schools](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Bottom_Schools.png)

The five lowest performing schools included Rodriguez High School, Figueroa High School, Huang High School, Hernandez High School, and Johnson High Schools, all of which were district schools with student counts between 2,917 and 4,761. 

## Average Math and Reading Scores by Grade
### Create Grade-Level DataFrames
To determine the average math and reading scores by grade I needed to create two new DataFrames, one to display the average math scores per grade and one to display the average reading scores per grade, both to be grouped by school and each grade within the DataFrames being a Series of data.
To start, I created a Series for each grade by filtering the school_data_complete_df DataFrame for each grade separately:
```
# Create a grade level DataFrames.
ninth_graders = school_data_complete_df[(school_data_complete_df["grade"]=="9th")]
tenth_graders = school_data_complete_df[(school_data_complete_df["grade"]=="10th")]
eleventh_graders = school_data_complete_df[(school_data_complete_df["grade"]=="11th")]
twelfth_graders = school_data_complete_df[(school_data_complete_df["grade"]=="12th")]
```

### *Score Averages Grouped by School Name*
To get the average math score by grade level for each school, I used the grouby() function on the “school_name” column for each grade level DataFrame and applied the mean() on the “math_score” column:
```
# Group each school Series by the school name for the average math score.
ninth_grade_math_scores = ninth_graders.groupby(["school_name"]).mean()["math_score"]
tenth_grade_math_scores = tenth_graders.groupby(["school_name"]).mean()["math_score"]
eleventh_grade_math_scores = eleventh_graders.groupby(["school_name"]).mean()["math_score"]
twelfth_grade_math_scores = twelfth_graders.groupby(["school_name"]).mean()["math_score"]
```
I replaced “math_score” with “reading_score” in the above code to get the average reading score by grade level for each school:
```
# Group each school Series by the school name for the average reading score.
ninth_grade_reading_scores = ninth_graders.groupby(["school_name"]).mean()["reading_score"]
tenth_grade_reading_scores = tenth_graders.groupby(["school_name"]).mean()["reading_score"]
eleventh_grade_reading_scores = eleventh_graders.groupby(["school_name"]).mean()["reading_score"]
twelfth_grade_reading_scores = twelfth_graders.groupby(["school_name"]).mean()["reading_score"]
```

### *Combine the Series into a DataFrame*
I created a new DataFrame named math_scores_by_grade and added the math scores for each grade to it using the following code:
```
# Combine each Series for average math scores by school into single DataFrame.
math_scores_by_grade = pd.DataFrame({
    "9th":ninth_grade_math_scores,
    "10th":tenth_grade_math_scores,
    "11th":eleventh_grade_math_scores,
    "12th":twelfth_grade_math_scores})
math_scores_by_grade.head()
```
The same format was used to create a new DataFrame that included all the average reading scores for each grade level:
```
# Combine each Series for average reading scores by school into single DataFrame.
reading_scores_by_grade = pd.DataFrame({
              "9th": ninth_grade_reading_scores,
              "10th": tenth_grade_reading_scores,
              "11th": eleventh_grade_reading_scores,
              "12th": twelfth_grade_reading_scores})
reading_scores_by_grade.head()
```

### *Format the Averages and Remove the Index Name*
I formatted the grade-level math average to one decimal place using the map(“{:.1f}”.format). I additionally removed the name of the index column school_name by setting the index name to “None”:
```
  # Format each grade column.
  math_scores_by_grade["9th"] = math_scores_by_grade["9th"].map("{:.1f}".format)
  math_scores_by_grade["10th"] = math_scores_by_grade["10th"].map("{:.1f}".format)
  math_scores_by_grade["11th"] = math_scores_by_grade["11th"].map("{:.1f}".format)
  math_scores_by_grade["12th"] = math_scores_by_grade["12th"].map("{:.1f}".format)
  # Remove the index name.
  math_scores_by_grade.index.name = None
  # Display the DataFrame.
  math_scores_by_grade.head()
```
![Math_Scores_by_Grade](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Math_Scores_by_Grade.png)

I applied the same formatting to the reading_scores_by_grade:
```
  # Format each grade column.
  reading_scores_by_grade["9th"] = reading_scores_by_grade["9th"].map("{:,.1f}".format)
  reading_scores_by_grade["10th"] = reading_scores_by_grade["10th"].map("{:,.1f}".format)
  reading_scores_by_grade["11th"] = reading_scores_by_grade["11th"].map("{:,.1f}".format)
  reading_scores_by_grade["12th"] = reading_scores_by_grade["12th"].map("{:,.1f}".format)
  # Remove the index name.
  reading_scores_by_grade.index.name = None
  # Display the data frame.
  reading_scores_by_grade.head()
```
![Reading_Scores_by_Grade](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Reading_Scores_by_Grade.png)

## Group Scores by School Spending per Student
Maria and her supervisor asked me to additionally determine how school spending per student affected the school’s average scores and passing percentages so that they could make decisions about the budget for the upcoming school year. School spending per student was to be sorted into four spending “bins”/ranges.
### *Establish the Spending Ranges per Student*
To determine which spending bins to use, I used the describe() method for the per_school_capita Series:
```
# Get the descriptive statistics for the per_school_capita.
per_school_capita.describe()
```
The descriptive statistics indicated that the minimum value was 578 and the maximum was 655. However, as only one school was at $578, $585 was selected as the lowest bin as four schools spent less than $585 per student. The standard deviation was 28.5, or about 30, and thus I increased the bins by $30 to get the four bins: at or below $585, $586-$615, $616-$645, and $646-$675.
Using the cut() function, I “cut” the per_school_capita into the spending_ranges using the following code:
```
# Cut the per_school_capita into the spending ranges.
spending_bins = [0, 585, 615, 645, 675]
pd.cut(per_school_capita, spending_bins)
```
I edited the above code using the groupby() function to group the spending bins as the index and used the count() method to determine how many schools were in each bin:
```
# Cut the per_school_capita into the spending ranges.
spending_bins = [0, 585, 615, 645, 675]
per_school_capita.groupby(pd.cut(per_school_capita, spending_bins)).count()
```
The output looked as follows:

![Spending_Bins_1](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Spending_Bins_1.png)

This output indicated that the schools were not grouped equally across the bins and thus the bins needed to be adjusted. The second range was increased to $585-$615 and the third range was decreased to $630-$645, with the above code being changed to the following:
```
# Cut the per_school_capita into the spending ranges.
spending_bins = [0, 585, 630, 645, 675]
per_school_capita.groupby(pd.cut(per_school_capita, spending_bins)).count()
```
The changes created a more equal distribution of schools within each range as shown in the following output:

![Spending_Bins_2](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Spending_Bins_2.png)

Once the ranges were set, each range was labeled using a list of string values:
```
# Establish the spending bins and group names.
spending_bins = [0, 585, 630, 645, 675]
group_names = ["<$584", "$585-629", "$630-644", "$645-675"]
```

### *Categorize the Spending Bins*
With the spending bins and ranges created, I created a new column in the per_school_summary_df DataFrame by using the cut() function on the per_school_capita Series to get the four spending bins and adding them to the per_school_summary_df DataFrame:
```
# Categorize spending based on the bins.
per_school_summary_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, spending_bins, labels=group_names)
per_school_summary_df
```

### *Group by the Spending Ranges*
Using the groupby() function on the “Spending Ranges (Per Student)” column and the mean() method, I created five new Series for the average math and reading scores, the percentage of students who passed math and reading, and the overall percentage of students who passed for each spending bin:
```
# Calculate averages for the desired columns.
spending_math_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Math Score"]

spending_reading_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]

spending_passing_math = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]

spending_passing_reading = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]

overall_passing_spending = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Overall Passing"]
```

### *Create a DataFrame for the Scores by School Spending*
Using these new Series, I created a new DataFrame:
```
# Assemble into DataFrame.
spending_summary_df = pd.DataFrame({
          "Average Math Score" : spending_math_scores,
          "Average Reading Score": spending_reading_scores,
          "% Passing Math": spending_passing_math,
          "% Passing Reading": spending_passing_reading,
          "% Overall Passing": overall_passing_spending})

spending_summary_df
```
Once the DataFrame was created, I formatted the average math and reading scores to one decimal place, the percentage passing math and reading to the nearest whole number, and the overall passing percentage to the nearest whole number:
```
# Formatting
spending_summary_df["Average Math Score"] = spending_summary_df["Average Math Score"].map("{:.1f}".format)

spending_summary_df["Average Reading Score"] = spending_summary_df["Average Reading Score"].map("{:.1f}".format)

spending_summary_df["% Passing Math"] = spending_summary_df["% Passing Math"].map("{:.0f}".format)

spending_summary_df["% Passing Reading"] = spending_summary_df["% Passing Reading"].map("{:.0f}".format)

spending_summary_df["% Overall Passing"] = spending_summary_df["% Overall Passing"].map("{:.0f}".format)

spending_summary_df
```
![Spending](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Spending.png)


## Group Scores by School Size
### *Create Bins for School Size*
Using similar methods as to what was used to create the spending bins, I created three bins by looking at the per_school_counts Series, with the “Small” bin including schools with fewer than 1,000 students, the “Medium” in including schools with 1,000-1,999 students, and the “Large” bin including 2,000-5,000 students:
```
# Establish the bins.
size_bins = [0, 1000, 2000, 5000]
group_names = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]
```

### *Categorize the Size Bins*
With the size bins created, I created a new column in the per_school_summary_df DataFrame by using the cut() function on the “Total Students” column within the per_school_summary_df DataFrame to get the three size bins and adding them to the per_school_summary_df DataFrame using the group_names:
```
# Categorize school size based on the bins.
per_school_summary_df["School Size"] = pd.cut(per_school_summary_df["Total Students"], size_bins, labels=group_names)

per_school_summary_df.head()
```

### *Group by School Size*
Using the groupby() function on the “School Size” column and the mean() method, I created five new Series for the average math and reading scores, the percentage of students who passed math and reading, and the overall percentage of students who passed for each school-size bin:
```
# Calculate averages for the desired columns.
size_math_scores = per_school_summary_df.groupby(["School Size"]).mean()["Average Math Score"]

size_reading_scores = per_school_summary_df.groupby(["School Size"]).mean()["Average Reading Score"]

size_passing_math = per_school_summary_df.groupby(["School Size"]).mean()["% Passing Math"]

size_passing_reading = per_school_summary_df.groupby(["School Size"]).mean()["% Passing Reading"]

size_overall_passing = per_school_summary_df.groupby(["School Size"]).mean()["% Overall Passing"]
```

### *Create a DataFrame for the Scores by School Size*
After the fives Series were created, I added them to a new DataFrame:
```
# Assemble into DataFrame.
size_summary_df = pd.DataFrame({
          "Average Math Score" : size_math_scores,
          "Average Reading Score": size_reading_scores,
          "% Passing Math": size_passing_math,
          "% Passing Reading": size_passing_reading,
          "% Overall Passing": size_overall_passing})

size_summary_df
```
Then, I formatted the average math and reading scores to one decimal place, the percentage passing math and reading to the nearest whole number, and the overall passing percentage to the nearest whole number within the size_summary_df DataFrame:
```
# Formatting.
size_summary_df["Average Math Score"] = size_summary_df["Average Math Score"].map("{:.1f}".format)

size_summary_df["Average Reading Score"] = size_summary_df["Average Reading Score"].map("{:.1f}".format)

size_summary_df["% Passing Math"] = size_summary_df["% Passing Math"].map("{:.0f}".format)

size_summary_df["% Passing Reading"] = size_summary_df["% Passing Reading"].map("{:.0f}".format)

size_summary_df["% Overall Passing"] = size_summary_df["% Overall Passing"].map("{:.0f}".format)

size_summary_df
```
![Size](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Size.png)

## Group Scores by School Type
I created five new series that calculated the average math and reading scores, math and reading passing percentages, and overall passing percentage for each school type, district or charter, by using the groupby() function to group the data on the “School Type” column within the per_school_summary_df DataFrame and the mean() method:
```
# Calculate averages for the desired columns.
type_math_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Math Score"]

type_reading_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Reading Score"]

type_passing_math = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Math"]

type_passing_reading = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Reading"]

type_overall_passing = per_school_summary_df.groupby(["School Type"]).mean()["% Overall Passing"]
```
Using these new Series, I created a new DataFrame called type_summary_df:
```
# Assemble into DataFrame.
type_summary_df = pd.DataFrame({
          "Average Math Score" : type_math_scores,
          "Average Reading Score": type_reading_scores,
          "% Passing Math": type_passing_math,
          "% Passing Reading": type_passing_reading,
          "% Overall Passing": type_overall_passing})

type_summary_df
```
Like the previous DataFrames, I formatted the average scores and percentages:
```
# Formatting
type_summary_df["Average Math Score"] = type_summary_df["Average Math Score"].map("{:.1f}".format)

type_summary_df["Average Reading Score"] = type_summary_df["Average Reading Score"].map("{:.1f}".format)

type_summary_df["% Passing Math"] = type_summary_df["% Passing Math"].map("{:.0f}".format)

type_summary_df["% Passing Reading"] = type_summary_df["% Passing Reading"].map("{:.0f}".format)

type_summary_df["% Overall Passing"] = type_summary_df["% Overall Passing"].map("{:.0f}".format)

type_summary_df
```
![Type](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Type.png)

# PyCity Schools Challenge
## Overview
### *Purpose*
Maria and her supervisor were notified by the school board that the students_complete.csv file showed evidence of academic dishonesty for the 9th grade reading and math scores at Thomas High School. Maria asked that I replace these scores with NaNs while keeping the rest of the data intact and repeating my school district analysis above to determine how these changes affected the overall analysis.

## Replace Ninth-Grade Reading and Math Scores
To start, I imported the NumPy module:
```
# Install numpy using conda install numpy or pip install numpy. 
# Step 1. Import numpy as np.
import numpy as np
```
Using the Pandas loc method, I added an opening parenthesis, used a comparison operator to retrieve all the rows with Thomas High School from the “school_name” column of the student_data_df DataFrame, and then closed the parenthesis. Adding the “&” logical operator and another opening parenthesis, I used a comparison operator to retrieve all the rows with ninth grade from the “grade” column of the student_data_df DataFrame, and then closed the parenthesis. After the closing parenthesis, I added a comma and the “reading_score” column. Finally, outside of the closing brackets of the loc method, I set the ninth grade reading scores for Thomas High School equal to np.nan:
```
# Step 2. Use the loc method on the student_data_df to select all the reading scores from the 9th grade at Thomas High School and replace them with NaN.
student_data_df.loc[(student_data_df["school_name"]=="Thomas High School") & (student_data_df["grade"]=="9th"), "reading_score"] = np.nan
```
Using the code above and replacing “reading_score” with “math_score”, I replaced the ninth-grade math scores for Thomas High School with NaNs:
```
#  Step 3. Refactor the code in Step 2 to replace the math scores with NaN.
student_data_df.loc[(student_data_df["school_name"]=="Thomas High School") & (student_data_df["grade"]=="9th"), "math_score"] = np.nan
```
Then, I checked that the grades were replaced in the student_data_df DataFrame:
```
#  Step 4. Check the student data for NaN's. 
student_data_df.tail(10)
```
The output for the last 10 rows of the DataFrame looked as follows:
![NaNs](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/NaNs.png)

## Repeat the School District Analysis
### *District Summary*
Using the loc method with logical and comparison operators, I obtained the student count for all ninth graders at Thomas High School from the school_data_complete_df DataFrame:
```
# Step 1. Get the number of students that are in ninth grade at Thomas High School.
# These students have no grades. 
Thomas_High_School_9_Count = school_data_complete_df.loc[(school_data_complete_df["school_name"]=="Thomas High School") & (student_data_df["grade"]=="9th")].count()["Student ID"]

# Get the total student count 
student_count = school_data_complete_df["Student ID"].count()

# Step 2. Subtract the number of students that are in ninth grade at Thomas High School from the total student count to get the new total student count.
student_count_new = student_count - Thomas_High_School_9_Count 
```
Using the new student count and the number of students who passed math and reading, I calculated the percentage of students who passed math and reading:
```
# Step 3. Calculate the passing percentages with the new total student count.
# Calculate the percentage that passed math.
passing_math_percentage = passing_math_count/ student_count_new *100

#Calculate the percentage that passed reading.
passing_reading_percentage = passing_reading_count/ student_count_new *100 
```
I additionally used the new student count and the number of students who passed both math and reading to calculate the overall passing percentage:
```
# Step 4. Calculate the overall passing percentage with new total student count.
overall_passing_percentage = overall_passing_math_reading_count / student_count_new * 100
```

### *School Summary*
Using the same code that was used to calculate the number of ninth graders at Thomas High School, I calculated the number of students in grades ten to twelve:
```
# Step 5.  Get the number of 10th-12th graders from Thomas High School (THS).
THS_tenth_graders = school_data_complete_df.loc[(school_data_complete_df["school_name"]=="Thomas High School") & (student_data_df["grade"]=="10th")].count()["Student ID"]

THS_eleventh_graders = school_data_complete_df.loc[(school_data_complete_df["school_name"]=="Thomas High School") & (student_data_df["grade"]=="11th")].count()["Student ID"]

THS_twelfth_graders = school_data_complete_df.loc[(school_data_complete_df["school_name"]=="Thomas High School") & (student_data_df["grade"]=="12th")].count()["Student ID"]

THS_students = THS_tenth_graders + THS_eleventh_graders + THS_twelfth_graders
```
Using the loc method, I created a new DataFrame that included the students passing math in grades ten to twelve from Thomas High School:
```
# Step 6. Get all the students passing math from THS
THS_passing_math = school_data_complete_df.loc[(school_data_complete_df["school_name"]=="Thomas High School") & (student_data_df["grade"]!="9th") & (school_data_complete_df["math_score"] >= 70)].count()["Student ID"]
```
The same method was used to obtain the number of students passing reading in grades ten to twelve from Thomas High School:
```
# Step 7. Get all the students passing reading from THS
THS_passing_reading = school_data_complete_df.loc[(school_data_complete_df["school_name"]=="Thomas High School") & (student_data_df["grade"]!="9th") & (school_data_complete_df["reading_score"] >= 70)].count()["Student ID"]
```
Using the method again and adding condition operators for passing both math and reading, I determined the number of students in grades ten to twelve that passed both math and reading at Thomas High School:
```
# Step 8. Get all the students passing math and reading from THS
THS_passing__math_reading = school_data_complete_df.loc[(school_data_complete_df["school_name"]=="Thomas High School") & (student_data_df["grade"]!="9th") & (school_data_complete_df["math_score"] >= 70) & (school_data_complete_df["reading_score"] >= 70)].count()["Student ID"]
```
The above values were then used to calculate the percentage of ten to twelfth graders who passed math and reading at Thomas High School:
``` 
# Step 9. Calculate the percentage of 10th-12th grade students passing math from Thomas High School. 
THS_passing_math_percentage = THS_passing_math / THS_students * 100

# Step 10. Calculate the percentage of 10th-12th grade students passing reading from Thomas High School.
THS_passing_reading_percentage = THS_passing_reading / THS_students * 100
```
The overall percentage of students who passed both math and reading was also calculated:
```
# Step 11. Calculate the overall passing percentage of 10th-12th grade from Thomas High School. 
THS_overall_percentage = THS_passing__math_reading / THS_students * 100
```
With these new values, I used the loc method to replace the “% Passing Math”, “% Passing Reading”, and “% Overall Passing” scores for Thomas High School in the per_school_summary_df DataFrame:
```
# Step 12. Replace the passing math percent for Thomas High School in the per_school_summary_df.
per_school_summary_df.loc[("Thomas High School", "% Passing Math")] = THS_passing_math_percentage

# Step 13. Replace the passing reading percentage for Thomas High School in the per_school_summary_df.
per_school_summary_df.loc[("Thomas High School", "% Passing Reading")] = THS_passing_reading_percentage

# Step 14. Replace the overall passing percentage for Thomas High School in the per_school_summary_df.
per_school_summary_df.loc[("Thomas High School", "% Overall Passing")] = THS_overall_percentage
```

### *Top 5 and Bottom 5 Performing Schools*
To determine the top 5 and bottom 5 performing schools, I used the same code as in my prior analyses:
```
# Sort and show top five schools.
top_schools = per_school_summary_df.sort_values(["% Overall Passing"], ascending=False)
top_schools.head()

# Sort and show top five schools.
bottom_schools = per_school_summary_df.sort_values(["% Overall Passing"], ascending=True)
bottom_schools.head() 
```

### *Average Math and Reading Scores for each Grade Level from each School*
To obtain the average math scores for each grade level from each school, I used the same code as in my prior analyses, first creating a series of scores by grade level using conditionals:
```
# Create a Series of scores by grade levels using conditionals.
ninth_graders = school_data_complete_df[(school_data_complete_df["grade"]=="9th")]
tenth_graders = school_data_complete_df[(school_data_complete_df["grade"]=="10th")]
eleventh_graders = school_data_complete_df[(school_data_complete_df["grade"]=="11th")]
twelfth_graders = school_data_complete_df[(school_data_complete_df["grade"]=="12th")]
```
Then, I used the grouby() function on the “school_name” column for each grade level DataFrame and applied the mean() on the “math_score” and “reading_scores” columns to get the average math and reading scores by grade level for each school:
```
# Group each school Series by the school name for the average math score.
ninth_grade_math_scores = ninth_graders.groupby(["school_name"]).mean()["math_score"]
tenth_grade_math_scores = tenth_graders.groupby(["school_name"]).mean()["math_score"]
eleventh_grade_math_scores = eleventh_graders.groupby(["school_name"]).mean()["math_score"]
twelfth_grade_math_scores = twelfth_graders.groupby(["school_name"]).mean()["math_score"]

# Group each school Series by the school name for the average reading score.
ninth_grade_reading_scores = ninth_graders.groupby(["school_name"]).mean()["reading_score"]
tenth_grade_reading_scores = tenth_graders.groupby(["school_name"]).mean()["reading_score"]
eleventh_grade_reading_scores = eleventh_graders.groupby(["school_name"]).mean()["reading_score"]
twelfth_grade_reading_scores = twelfth_graders.groupby(["school_name"]).mean()["reading_score"]
```
Once the Series were created, I combined them into two DataFrames, one for the math scores and one for the reading scores:
```
# Combine each Series for average math scores by school into single data frame.
math_scores_by_grade = pd.DataFrame({
    "9th":ninth_grade_math_scores,
    "10th":tenth_grade_math_scores,
    "11th":eleventh_grade_math_scores,
    "12th":twelfth_grade_math_scores})
# Combine each Series for average reading scores by school into single data frame.
reading_scores_by_grade = pd.DataFrame({
              "9th": ninth_grade_reading_scores,
              "10th": tenth_grade_reading_scores,
              "11th": eleventh_grade_reading_scores,
              "12th": twelfth_grade_reading_scores})
```
I formatted the grade-level averages to one decimal place using the map(“{:.1f}”.format). I additionally removed the name of the index column school_name by setting the index name to “None”:
```
# Format each grade column.
math_scores_by_grade["9th"] = math_scores_by_grade["9th"].map("{:.1f}".format)
math_scores_by_grade["10th"] = math_scores_by_grade["10th"].map("{:.1f}".format)
math_scores_by_grade["11th"] = math_scores_by_grade["11th"].map("{:.1f}".format)
math_scores_by_grade["12th"] = math_scores_by_grade["12th"].map("{:.1f}".format)
# Remove the index name.
  math_scores_by_grade.index.name = None
  # Display the DataFrame.
  math_scores_by_grade.head()
```
I applied the same formatting to the reading_scores_by_grade:
```
  # Format each grade column.
  reading_scores_by_grade["9th"] = reading_scores_by_grade["9th"].map("{:,.1f}".format)
  reading_scores_by_grade["10th"] = reading_scores_by_grade["10th"].map("{:,.1f}".format)
  reading_scores_by_grade["11th"] = reading_scores_by_grade["11th"].map("{:,.1f}".format)
  reading_scores_by_grade["12th"] = reading_scores_by_grade["12th"].map("{:,.1f}".format)
  # Remove the index name.
  reading_scores_by_grade.index.name = None
  # Display the data frame.
  reading_scores_by_grade.head()
```
Then, I categorized spending into the four bins using the cut() method and created a new column in my per_school_summary_df DataFrame:
```
# Categorize spending based on the bins.
per_school_summary_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, spending_bins, labels=group_names)
```
Next, I created five new Series for the average math and reading scores, the percentage of students who passed math and reading, and the overall percentage of students who passed for each spending bin:
```
# Calculate averages for the desired columns. 
spending_math_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Math Score"]
spending_reading_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]
spending_passing_math = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]
spending_passing_reading = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]
overall_passing_spending = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Overall Passing"]
```
### *Scores by School Spending per Student*
I utilized the same spending bins and group names as in my original analysis to determine the passing percentages by spending per student:
```
# Establish the spending bins and group names.
spending_bins = [0, 585, 630, 645, 675]
group_names = ["<$584", "$585-629", "$630-644", "$645-675"]
```
These new Series were placed in a new DataFrame and the DataFrame was formatted:
```
# Create the DataFrame
spending_summary_df = pd.DataFrame({
          "Average Math Score" : spending_math_scores,
          "Average Reading Score": spending_reading_scores,
          "% Passing Math": spending_passing_math,
          "% Passing Reading": spending_passing_reading,
          "% Overall Passing": overall_passing_spending})

# Format the DataFrame 
spending_summary_df["Average Math Score"] = spending_summary_df["Average Math Score"].map("{:.1f}".format)

spending_summary_df["Average Reading Score"] = spending_summary_df["Average Reading Score"].map("{:.1f}".format)

spending_summary_df["% Passing Math"] = spending_summary_df["% Passing Math"].map("{:.0f}".format)

spending_summary_df["% Passing Reading"] = spending_summary_df["% Passing Reading"].map("{:.0f}".format)

spending_summary_df["% Overall Passing"] = spending_summary_df["% Overall Passing"].map("{:.0f}".format)

spending_summary_df
```

### *Scores by School Size*
Again, using the code from my original analyses, I established the school size bins and categorized schools into the new bins:
```
# Establish the bins.
size_bins = [0, 1000, 2000, 5000]
group_names = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

# Categorize school size based on the bins.
per_school_summary_df["School Size"] = pd.cut(per_school_summary_df["Total Students"], size_bins, labels=group_names)
```
Next, I created five new Series for the average math and reading scores, the percentage of students who passed math and reading, and the overall percentage of students who passed for each school-size bin:
```
# Calculate averages for the desired columns. 
size_math_scores = per_school_summary_df.groupby(["School Size"]).mean()["Average Math Score"]

size_reading_scores = per_school_summary_df.groupby(["School Size"]).mean()["Average Reading Score"]

size_passing_math = per_school_summary_df.groupby(["School Size"]).mean()["% Passing Math"]

size_passing_reading = per_school_summary_df.groupby(["School Size"]).mean()["% Passing Reading"]

size_overall_passing = per_school_summary_df.groupby(["School Size"]).mean()["% Overall Passing"]
```
These Series were assembled into a new DataFrame and the DataFrame was formatted:
```
# Assemble into DataFrame. 
size_summary_df = pd.DataFrame({
          "Average Math Score" : size_math_scores,
          "Average Reading Score": size_reading_scores,
          "% Passing Math": size_passing_math,
          "% Passing Reading": size_passing_reading,
          "% Overall Passing": size_overall_passing})

# Format the DataFrame  
size_summary_df["Average Math Score"] = size_summary_df["Average Math Score"].map("{:.1f}".format)

size_summary_df["Average Reading Score"] = size_summary_df["Average Reading Score"].map("{:.1f}".format)

size_summary_df["% Passing Math"] = size_summary_df["% Passing Math"].map("{:.0f}".format)

size_summary_df["% Passing Reading"] = size_summary_df["% Passing Reading"].map("{:.0f}".format)

size_summary_df["% Overall Passing"] = size_summary_df["% Overall Passing"].map("{:.0f}".format)

size_summary_df
```

### *Scores by School Type*

Using the same methods as above, I created five new series that calculated the average math and reading scores, math and reading passing percentages, and overall passing percentage for each school type, district or charter:
```
# Calculate averages for the desired columns. 
type_math_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Math Score"]

type_reading_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Reading Score"]

type_passing_math = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Math"]

type_passing_reading = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Reading"]

type_overall_passing = per_school_summary_df.groupby(["School Type"]).mean()["% Overall Passing"]
```
These Series were assembled into a new DataFrame and the DataFrame was formatted:

```
# Assemble into DataFrame. 
type_summary_df = pd.DataFrame({
          "Average Math Score" : type_math_scores,
          "Average Reading Score": type_reading_scores,
          "% Passing Math": type_passing_math,
          "% Passing Reading": type_passing_reading,
          "% Overall Passing": type_overall_passing})

# Format the DataFrame 
type_summary_df["Average Math Score"] = type_summary_df["Average Math Score"].map("{:.1f}".format)

type_summary_df["Average Reading Score"] = type_summary_df["Average Reading Score"].map("{:.1f}".format)

type_summary_df["% Passing Math"] = type_summary_df["% Passing Math"].map("{:.0f}".format)

type_summary_df["% Passing Reading"] = type_summary_df["% Passing Reading"].map("{:.0f}".format)

type_summary_df["% Overall Passing"] = type_summary_df["% Overall Passing"].map("{:.0f}".format)

type_summary_df
```

## Results
1. How is the district summary affected?
Original Dataset:

![District_Summary_1](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/District_Summary_1.png)

Refactored Dataset:

![ District_Summary_2](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/District_Summary_2.png)

Replacing the ninth-grade scores for Thomas High School with NaNs decreased the average math score by 0.1%-points, while the reading score remained the same. The percent of students passing math decreased from 75.0% to 74.8%, the percentage of students who passing reading decreased slightly from 85.8% to 85.7%, and the percentage of students who passed both decreased from 65.2% to 64.9%. 

2. How is the school summary affected?
Original Dataset:

![School_Summary_1](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/School_Summary_1.png)

Refactored Dataset:

![ School_Summary_2](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/School_Summary_2.png)

As expected, the math and reading scores for Thomas High School changed for the school summary, while math and reading scores for other high schools remained the same. For Thomas High School, the average math score declined from 83.41 to 83.35, the average reading score slightly increased from 83.84 to 83.89, the percentage of students passing math slightly declined from 93.27% to 93.19%, the percentage of students passing reading declined from 97.30% to 97.02%, and the percentage of students passing both declined from 90.94% to 90.63%. 

3. How does replacing the ninth graders’ math and reading scores affect Thomas High School’s performance relative to the other schools?
Original Dataset:

![Top5_Schools_1](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Top5_Schools_1.png)

Refactored Dataset:

![ Top5_Schools_2](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Top5_Schools_2.png)

Replacing the ninth graders’ math and reading scores did not affect Thomas High School’s performance relative to the other schools. Thomas High School remained the second-highest performing school.

4. How does replacing the ninth-grade scores affect the following:
  - Math and reading scores by grade

Original Dataset:

*Math*

![Math_by_Grade_1](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Math_by_Grade_1.png)

*Reading*

![Reading_by_Grade_1](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Reading_by_Grade_1.png)

Refactored Dataset:

*Math*

![Math_by_Grade_2](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Math_by_Grade_2.png)

*Reading*

![Reading_by_Grade_2](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Reading_by_Grade_2.png)

Replacing the ninth-grade scores did not affect the math and reading scores by grade. 

  - Scores by school spending

Original Dataset:

![Spending_Bins_1](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Spending_Bins_1.png)

Refactored Dataset:

![Spending_Bins_2](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Spending_Bins_2.png)

Replacing the ninth-grade scores did not affect the math and reading scores by school spending. 

  - Scores by school size

Original Dataset:

![Size_1](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Size_1.png)

Refactored Dataset:

![Size_2](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Size_2.png)

Replacing the ninth-grade scores did not affect the math and reading scores by school size. 

  - Scores by school type

Original Dataset:

![Type_1](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Type_1.png)

Refactored Dataset:

![Type_2](https://github.com/kcharb7/School_District_Analysis/blob/main/Resources/Type_2.png)

Replacing the ninth-grade scores did not affect the math and reading scores by school type. 

## Summary
After replacing the ninth-grade math and reading scores for Thomas High School with NaNs, the average math scores, the percentage of students who passed math, the percentage of students who passed reading, and the percentage of students who passed both declined in both the district and school summaries. Contrarily, the average reading score for Thomas High School increased in the school summary. However, despite these changes, Thomas High School remained the second-highest performing school. Replacing the ninth-grade scores did not affect the math and reading scores by grade, school spending, school size, or school type. 
