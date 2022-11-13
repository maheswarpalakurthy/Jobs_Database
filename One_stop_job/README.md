One Stop Job (addition to Job database)

Team members: Namitha J C (NUID – 002795461)
		 Sinchana Kumara (NUID – 002780971)
		 Maheswara Sai Ram Palakurthy (NUID – 002772768)

Github Id’s: Njc27
	       SinchanaKumara
	       maheswarpalakurthy

ReadMe Document: 

The objective of this project is to create a database for job finders where they need to type in the job role and select from which job sites they will apply for that job. The database will consist data from the most visited job sites like Indeed, Glassdoor, Monster etc., with columns Job Id, Job role, Job Description, Skills, Company, Location, Job site (link to the site). 

Using BeautifulSoup in python, we scrape the necessary data required and store them in a csv file. Later this csv file is cleaned from duplicate and null values. By then storing this data in a sql table we can perform the necessary operations such as : Searching and Filtering the data.



##Assignment - 2

A model of job database – one stop job using Twitter:

ER diagram:

<img width="468" alt="image" src="https://user-images.githubusercontent.com/113729244/201505723-2c050c31-259b-4f52-a819-f70a4f2def0f.png">


The osj account has a login and password. This login is the same as a user’s Twitter handle. The Twitter handle is unique – hence it can also be treated as the primary key of the table.

A user can apply to a job through Twitter by applying through ‘job_url’. This job URL mentioned in a tweet is stored in ‘tweet_url’ table. Every tweet that has a URL in it, will have an entry in ‘tweet_url’ table.

‘jobs_applied’ has the ‘application_tweet_id’ of the tweet which uniquely distinguishes each tweet, ‘job_url’ which is a foreign key reference to the ‘job_url’ in ‘tweet_url’ table.

A user can tweet (save) how many ever jobs he/she wants and add them to ‘my_saved_applications’. Hence ‘my_saved_applications’ has a composite key with is a combination of both ‘job_tweet_id’ and ‘user_handle’ in the table.


UML diagram:

<img width="508" alt="image" src="https://user-images.githubusercontent.com/113729244/201505797-7f5bc66f-76a6-451c-9a33-08d6c83e09b6.png">

SQL Statements for the conceptual model:

•tweets_table:

 CREATE TABLE `tweets_table` (
  `tweet_id` bigint NOT NULL,
  `recruiter_twitter_handle` varchar(64) DEFAULT NULL,
  `tweet_text` varchar(1000) DEFAULT NULL,
  `tweet_date` timestamp NULL DEFAULT NULL,
  `profile_image_url` varchar(256) DEFAULT NULL,
  `recruiter_tweet_location` varchar(50) DEFAULT NULL,
 PRIMARY KEY (`tweet_id`) );
 
•tweets_tags:

 CREATE TABLE `tweet_tags` (
  `tweet_id` bigint NOT NULL,
  `tags` varchar(256) DEFAULT NULL,
   PRIMARY KEY (`tweet_id`),
   CONSTRAINT `USER_TAG_CONSTRAINT` FOREIGN KEY (`tweet_id`) REFERENCES `tweets_table` (`tweet_id`) );

•job_urls:

 CREATE TABLE `job_urls` (
  `tweet_id` bigint DEFAULT NULL,
  `job_url` varchar(256) NOT NULL,
   PRIMARY KEY (`job_url`),
   KEY `TWEET_URL_CONSTRAINT` (`tweet_id`),
  CONSTRAINT `TWEET_URL_CONSTRAINT` FOREIGN KEY (`tweet_id`) REFERENCES `tweets_table` (`tweet_id`) );

•osj_acount:

CREATE TABLE `osj_account` (
  `twitter_handle` varchar(256) NOT NULL,
  `password` varchar(256) DEFAULT NULL,
  `role` varchar(128) DEFAULT NULL,
  PRIMARY KEY (`twitter_handle`) );

•osj_users:

CREATE TABLE `osj_users` (
  `twitter_handle` varchar(256) NOT NULL,
  `screen_name` varchar(256) DEFAULT NULL,
  `description` varchar(1024) DEFAULT NULL,
  `location` varchar(128) DEFAULT NULL,
  `joined_date` timestamp NULL DEFAULT NULL,
  `user_id` varchar(128) DEFAULT NULL,
  PRIMARY KEY (`twitter_handle`),
  CONSTRAINT `USER_CONSTRAINT` FOREIGN KEY (`twitter_handle`) REFERENCES `osj_account` (`twitter_handle`) );

•my_saved_applications:

CREATE TABLE `my_saved_applications` (
  `job_tweet_id` bigint NOT NULL,
  `recruiter_twitter_handle` varchar(256) DEFAULT NULL,
  `user_handle` varchar(256) NOT NULL,
  `post_date` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`job_tweet_id`,`user_handle`),
  KEY `SAVED_URL_CONSTRAINT` (`user_handle`),
  CONSTRAINT `JOB_TWEET_ID_CONSTRAINT` FOREIGN KEY (`job_tweet_id`) REFERENCES `tweets_table` (`tweet_id`) );

•jobs_applied:

CREATE TABLE `jobs_applied` (
  `application_tweet_id` bigint NOT NULL,
  `job_url` varchar(256) DEFAULT NULL,
  `user_twitter_handle` varchar(256) DEFAULT NULL,
  `applied_tweet_date` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`application_tweet_id`),
  KEY `JOBS_APPLIED_CONSTRAINT` (`job_url`),
  CONSTRAINT `JOBS_APPLIED_CONSTRAINT` FOREIGN KEY (`job_url`) REFERENCES `job_urls` (`job_url`) );

USE CASE, SQL QUERIES and RELATIONAL ALGEBRA:

1. MAHESWARA SAI RAM PALAKURTHY
1. Select t.recruiter_twitter_handle, t.tweet_text, t.tweet_date, t.profile_image_url, j.job_url from tweets_table AS t, job_urls AS j where recruiter_twitter_handle = 'SAICjobs' AND t.tweet_id = j.tweet_id;

Use Case: View the details of tweeted handle, such as handle-name ,text ,tweeted date, user-profile-image-url and links present in the tweet posted by “SAICjobs”.
Description: User views the list of data related to the tweet posted by  “#SAICjobs”.
Actor: User
Precondition: User “SAICjobs” should be present and should have posted tweets.
Steps:
Actor action: User views the list of tweet data posted by “SAICjobs”.
System Responses: The list of data for all the tweets posted by “SAICjobs” are displayed.
Alternate Path: There are no orders made by a user.
Error: No history of orders available.

π t . recruiter_twitter_handle, t . tweet_text, t . tweet_date, t . profile_image_url, j . job_url
 σ recruiter_twitter_handle = "SAICjobs" AND t . tweet_id = j . tweet_id
  (ρ t tweets_table ×
   ρ j job_urls)

 <img width="348" alt="image" src="https://user-images.githubusercontent.com/98120682/201506384-56ced227-39c8-4337-b601-3fb25ff33654.png">



2.	Select A.twitter_handle, B.recruiter_twitter_handle, A.location from osj_users A, tweets_table B where A.twitter_handle <> B.recruiter_twitter_handle and A.location = B.recruiter_tweet_location ORDER BY A.location;

Use Case: Displaying all the tweets with the same location as the User.
Description: User view tweets from his locality.
Actor: User
Precondition: Jobs must be present in the user location.
Steps:
Actor action: View Jobs with location same as the User.
System Responses: The list of Jobs based on the same location as the user are displayed.
Alternate Path: There are no latest jobs postings.
Error: No Jobs available from the given location.

τ a . location
 π a . twitter_handle, b . recruiter_twitter_handle, a . location
  σ a . twitter_handle <> b . recruiter_twitter_handle AND a . location = b . recruiter_tweet_location
   (ρ a osj_users ×
    ρ b tweets_table)

 <img width="366" alt="image" src="https://user-images.githubusercontent.com/98120682/201506390-11f326a2-8a1d-43ec-96bd-92d131dd7a5f.png">



3.	Select job_url From job_urls Where tweet_id IN (Select tweet_id From tweets_table where tweet_id IN (Select tweet_id from tweet_tags where tags = '#databaseJobs'));

Use Case: View the links attached to the tweet with the hashtag “#databaseJobs”
Description: User views the links related to the hashtag “#databaseJobs”
Actor: User
Precondition: Tweets must be present with the above hashtag.
Steps:
Actor action: User views all the links for a particular hashtag.
System Responses: The links for the Tweets with hashtags.
Alternate Path: There are no orders made by a user.
Error: No history of orders available.

π job_url
 σ tweet_id IN  (π tweet_id
 σ tweet_id IN (π tweet_id
 σ tags = "#databaseJobs" tweet_tags) tweets_table) job_urls

 
<img width="380" alt="image" src="https://user-images.githubusercontent.com/98120682/201506395-fcf6fde8-ee42-4411-aef7-7697256cb839.png">


4.	Select * from tweets_table where tweet_text LIKE '%database%';

Use Case: Display all the jobs with keywords “database” in them.
Description: User can view and search  all the job postings with keyword “database” in them.
Actor: User
Precondition: Jobs with keyword “database” must be present .
Steps:
Actor action: User can view Jobs based on keyword “database”.
System Responses: Display all the job postings with keyword “database”.
Alternate Path: Job postings are shown as null or none.
Error: Jobs data unavailable at the moment.

σ tweet_text LIKE "%database%" tweets_table
 
<img width="382" alt="image" src="https://user-images.githubusercontent.com/98120682/201506397-00511216-5164-44b5-b24b-01f98336c355.png">


5.	 Select * from tweets_table where tweet_date >= NOW() - INTERVAL 2 DAY;

Use Case: Display all the job postings from the last 2 days.
Description: Job postings from the last 2 days are displayed.
Actor: User
Precondition: There must be jobs posted in the last 2 days.
Steps:
Actor action: User can filter Jobs based on the last 48 hours.
System Responses: Display the Jobs for the last 2 days.
Alternate Path: No Jobs posted in the given days.
Error: No Jobs available now.

σ tweet_date = π date σ date - now() tweets_table
 
![image](https://user-images.githubusercontent.com/98120682/201506362-10fd1db1-4225-4b15-ab8d-4bf24453f24f.png)

 
2. NAMITHA J C

1. Select DISTINCT recruiter_twitter_handle from tweets_table order by recruiter_twitter_handle;

Use Case: View the recruiter handles in the database.
Description: User views all the recruiter handles present in the database.
Actor: User
Precondition: Database must of data for the twitter handles.
Steps:
Actor action: User views all the recruiter handles.
System Responses: The list of all recruiter handles are displayed to user.
Alternate Path: There are no orders made by a user.
Error: No history of orders available.

δ
 τ recruiter_twitter_handle
  π recruiter_twitter_handle tweets_table
  
  <img width="398" alt="image" src="https://user-images.githubusercontent.com/113727586/201506244-143db2ea-587e-4c9d-b0a6-ada43314c45b.png">

2. Select * from tweets_table where tweet_id IN (Select tweet_id from tweet_tags where tags = '#paidinternships');

Use Case: View the tweets attached to the tweet with the hashtag “#paidinternships'”
Description: User views the tweets related to the hashtag “#paidinternships'”
Actor: User
Precondition: Tweets must be present with the above hashtag.
Steps:
Actor action: User views all the links for a particular hashtag.
System Responses: The list for the Tweets with hashtags.
Alternate Path: There are no orders made by a user.
Error: No history of orders available.

σ tweet_id IN (π tweet_id
 σ tags = "#paidinternships" tweet_tags)  tweets_table

<img width="410" alt="image" src="https://user-images.githubusercontent.com/113727586/201506271-5345eb9d-7e2f-4ad3-80f3-4353bf8ca829.png">

3.	Select t.recruiter_twitter_handle, Count(application_tweet_id) AS 'NO. Of Applications' from tweets_table as t RIGHT JOIN job_urls as j ON t.tweet_id = j.tweet_id RIGHT JOIN jobs_applied as ja ON ja.application_tweet_id = j.tweet_id Group BY t.recruiter_twitter_handle;

3.Use Case: Displaying the user Applied job count with respect to recruiter.
Description: User can Applied job count with respect to recruiter.
Actor: User
Precondition: User must have completed Job applications.
Steps:
Actor action: View Applied Job count and its recruiter.
System Responses: The Count of Applications completed with respect to recruiter is displayed.
Alternate Path: User has not applied to any jobs.
Error: Completed applications are not available as of now.

π t . recruiter_twitter_handle, COUNT (application_tweet_id)
 γ COUNT (application_tweet_id) tweets_table  as no_of_Applications
(ρ t tweets_table ⋈ t . tweet_id = j . tweet_id
   ρ j job_urls)

<img width="404" alt="image" src="https://user-images.githubusercontent.com/113727586/201506281-e890ed96-8962-4f72-a7c6-3deb8312b3c4.png">

4.	Select * from osj_users where joined_date between '2022-11-09' and '2022-11-13';

Use Case: Display users who have joined between November 9th 2022 and November 13th 2022.
Description: Filter users based on joined date.
Actor: Admin
Precondition: User must have the field Joined Date.
Steps:
Actor action: Admin can filter users based on joined date.
System Responses: Display all the users joined between the specified date.
Alternate Path: No Users are Displayed.
Error: User Data unavailable.

σ "2022-11-09" <= joined_date AND joined_date <= "2022-11-13" osj_users

<img width="395" alt="image" src="https://user-images.githubusercontent.com/113727586/201506298-2b835587-22ca-44e0-ac61-9a6cec84e454.png">

5.	Select * from tweets_table where recruiter_tweet_location LIKE '%New York%';

Use Case: Enable user to search recruiter location.
Description: Display users the all the recruiter based on a location.
Actor: User
Precondition: Recruiter location must be present.
Steps:
Actor action: User can view recruiters based on a location.
System Responses: Display all the recruiters from the search location.
Alternate Path: No Recruiters are displayed.
Error: Recruiter data currently unavailable.

π j . job_url, t . recruiter_twitter_handle
 σ j . tweet_id = t . tweet_id
  (ρ j job_urls ×
   ρ t tweets_table)
<img width="404" alt="image" src="https://user-images.githubusercontent.com/113727586/201506310-a5dea3e1-1a04-47c3-bbee-081214518772.png">





