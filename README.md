Database Design
Tables:

Users - Stores information about users (students).

user_id (PK)
name
email
phone
joined_date

CodeKata - Tracks problems solved by each user.
user_id (FK)
problems_solved

Attendance - Tracks daily attendance of users.
attendance_id (PK)
user_id (FK)
date
status (Present or Absent)

Topics - Stores topics covered on specific dates.
topic_id (PK)
name
date_covered

Tasks - Stores tasks assigned to users.
task_id (PK)
topic_id (FK)
user_id (FK)
submission_date
status (Submitted or Not Submitted)

CompanyDrives - Stores information about company drives.
drive_id (PK)
company_name
drive_date

Mentors - Stores information about mentors and their mentees.
mentor_id (PK)
name

Mentees - Tracks mentor-mentee relationships.
mentor_id (FK)
user_id (FK)



1..Find all the topics and tasks taught in October:

sql

SELECT Topics.name AS Topic, Tasks.task_id AS Task
FROM Topics
LEFT JOIN Tasks ON Topics.topic_id = Tasks.topic_id
WHERE MONTH(Topics.date_covered) = 10;

Expected Output:
+---------------+-------+
| Topic         | Task  |
+---------------+-------+
| SQL Basics    | 1     |
| HTML & CSS    | 2     |
| JavaScript    | 3     |
| Python Intro  | 4     |
+---------------+-------+

2.Find all company drives between 15-Oct-2020 and 31-Oct-2020:

sql

SELECT * FROM CompanyDrives
WHERE drive_date BETWEEN '2020-10-15' AND '2020-10-31';

Expected Output:
+----------+---------------+------------+
| drive_id | company_name  | drive_date |
+----------+---------------+------------+
| 1        | Google        | 2020-10-16 |
| 2        | Amazon        | 2020-10-20 |
| 3        | Facebook      | 2020-10-25 |
+----------+---------------+------------+

3.Find all company drives and students who appeared for placement:

sql

SELECT CompanyDrives.company_name, Users.name AS Student
FROM CompanyDrives
INNER JOIN Attendance ON Attendance.date = CompanyDrives.drive_date AND Attendance.status = 'Present'
INNER JOIN Users ON Users.user_id = Attendance.user_id;

Expected Output:
+---------------+---------+
| company_name  | Student |
+---------------+---------+
| Google        | Alice   |
| Amazon        | Charlie |
+---------------+---------+

4.Find the number of problems solved by each user in CodeKata:

sql

SELECT Users.name, CodeKata.problems_solved
FROM Users
INNER JOIN CodeKata ON Users.user_id = CodeKata.user_id;

Expected Output:
+---------+----------------+
| name    | problems_solved|
+---------+----------------+
| Alice   | 120            |
| Bob     | 85             |
| Charlie | 150            |
+---------+----------------+

5.Find all mentors with more than 15 mentees:

sql

SELECT Mentors.name, COUNT(Mentees.user_id) AS Mentee_Count
FROM Mentors
INNER JOIN Mentees ON Mentors.mentor_id = Mentees.mentor_id
GROUP BY Mentors.mentor_id
HAVING Mentee_Count > 15;

Expected Output:
Since no mentor has more than 15 mentees in the sample data, this would return an empty set


6.Find the number of users who were absent and did not submit tasks between 15-Oct-2020 and 31-Oct-2020:

sql

SELECT COUNT(DISTINCT Users.user_id) AS Absent_NoTask
FROM Users
INNER JOIN Attendance ON Users.user_id = Attendance.user_id
LEFT JOIN Tasks ON Users.user_id = Tasks.user_id
    AND Tasks.submission_date BETWEEN '2020-10-15' AND '2020-10-31'
WHERE Attendance.date BETWEEN '2020-10-15' AND '2020-10-31'
    AND Attendance.status = 'Absent'
    AND (Tasks.status IS NULL OR Tasks.status = 'Not Submitted');
    
Expected Output:
+---------------+
| Absent_NoTask |
+---------------+
| 1             |
+---------------+
