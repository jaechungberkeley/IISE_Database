# Project: UC Berkeley IISE Alumni Mentorship Program Database System
## Client Description and Objective
Institute of Industrial and Systems Engineers (IISE) at UC Berkeley is the largest IEOR and ORMS student organization on the UC Berkeley campus. The club is driven to provide students with academic and professional resources. This includes fun events and programs aimed to help students build their professional and personal skills. We focus on building a database prototype for the Alumni Mentorship program within IISE, which aims to provide professional and academic growth opportunities through mentorship.
IISE hopes to have a centralized database containing information about alumni, alumni mentors, student mentees, and member participation in program events. This would create a more organized way of tracking alumni presence to aid efficient outreach and serve as a networking tool specific to IEOR and ORMS students looking to connect with alumni in industries, roles, and skills that they are interested in. This database would also allow IISE to track alumni engagement year over year, which could become a tool used to perform analytics on the performance of the program.

## EER Diagram and Relational Schema
The EER diagram was created with the purpose of capturing how each component of this mentorship program is meant to interact internally and externally with outside academic & professional networks. This included adding entities such as Person, Event, Skill, Opportunity, Company, and Feedback, with relations and attributes to capture the data that IISE currently collects or is planning to collect in the future to effectively analyze the program. Using the process for converting an EER diagram to a relational schema, we created 37 relations and implemented the schema to MySQL.

## Normalization
All of our relations are in first normal form since the process of converting an EER diagram to relational schema creates a new relation for each multivalued attribute. The majority of our relations are also in second normal form; the notable exception being the Event relation (3). Most of our relations are also in third normal form, with the exception of the Internship_Externship relation (2a), which is in second normal form but not in third normal form. The majority of our relations are also in Boyce-Codd normal form, with the exception of the Corporate_Info_Session relation (3d), which is in third normal form but not in Boyce-Codd normal form. Thus, for most relations, the functional dependencies are simply that all primary keys determine the non-primary keys.

## Synthetic Data
Our queries call 10 tables from our database: Person, Big_Student, Current_Student, Alumni, Skill, Program_Feedback, Alumni_has_Skill, Current_Student_has_Skill, Club_Mentee_Check_In, and Mentorship. These tables are used in the following queries and analytical models built. For attributes not relevant for our models such as name, email address, and more, we randomly generated data using Python packages like Faker and random. For tables Mentorship, Program_Feedback, Club_Mentee_Check_In, Alumni_has_Skill, Current_Student_has_Skill, and Skill, solely using random generation was not sufficient, because we needed to ensure that the information across these tables matched to allow effective model building and results. For example, it would be crucial for the mentors, mentees, and mentorship pairings in Mentorship, Program_Feedback, and Club_Mentee_Check_In to be consistent in order to execute Query 1 and build its analytical model successfully. Other instances apply, so a good amount of manual adjustments and matching were made.
Some attributes that were significant to model building were not yet introduced into the schema, which lead to additions of new attributes into existing relations and creating new relations to capture the wanted information. Synthetic data was also created for these new tables and attributes. 
Once we created reasonable synthetic data useful for modeling, we imported these pandas dataframes into our SQL database. We often encountered database corruption when the model failed to forward engineer correctly in MySQL. This lead to multiple attempts to import data into MySQL before succeeding. 

## Queries and Models
IISE requested that we build our interesting queries with the goal of evaluating the mentorship program that benefits the members the most. Our team interpreted this goal using two different angles. The first is to evaluate the direct feedback the members provide the club. This includes the data of mentee check-ins in long description format and data of mentee and mentor ratings. The second is to infer the increase in skill-level members gained after the program and the main contributing factors of this. Hence, we formulated our interesting queries surrounding these two questions.

Query 1: How do we evaluate the overall satisfaction of the mentorship based on ratings? How can we interpret the feedback from mentees?
IISE gathers constant feedback from its members in order to improve and iterate upon its mentorship program. To provide the best experience for its members, IISE collects two types of feedback data:

	1. Quantitative feedback in the form of ratings from 1-10
	2. Qualitative feedback from mentees during check-ins with club team members
	
The quantitative ratings are important because they allow us to find correlations and key trends in an attempt to measure member satisfaction with the program. These qualitative feedback taken from check-ins will help the club identify key topics of satisfaction or dissatisfaction. Our team built a SQL query that aggregates mentee feedback descriptions, mentor ratings, and mentee ratings. We then loaded the table into a Jupyter notebook to perform analysis on it.
	Our main form of analysis for the quantitative data is to fit a linear line for the mentee and mentor ratings (Figure 1). This helps visualize trends and find correlations among mentorship pairings. To do this, we used the seaborn package to graph a line between our independent variable (Mentee Rating) and dependent variable (Mentor Rating). Interpreting our results, we can see an overall positive trend between Mentor and Mentee ratings. This makes sense because the experiences between each mentee and mentor pairing are often correlated. 
The second analytical tool is to use natural language processing techniques to find the most positive and negative sentiments from the qualitative feedback. To achieve this, we assign a polarity score to each individual word according to the VADER lexicon index. VADER (Valence Aware Dictionary and Sentiment Reasoner) is a lexicon and rule-based sentiment analysis tool.
Our team is specifically analyzing the most positive and negative sentiments from these text-based program feedbacks so our client is able to identify what worked well and ideas of improvement for their mentorship program. Here are our top 3 most positive comments and top 3 most negative comments:

	Most Positive:

	1. I loved it! It was a great opportunity to connect with successful IEOR alumni.
	2. Definitely gained a lot of professional help with interviews. Really liked it!
	3. I developed a strong bond with my mentor and they were very helpful in giving post-graduation advice!
	
	Most Negative:

	1. I truly disliked my experience and don't want to return again. It was not helpful.
	2. I think the program took too much time and didn't offer as much as I thought it would have. I would join again if it had more benefits.
	3. The program was kinda useful, but didn't stand out very much. I wish it took less time.
	
Interpreting the positive results, we can see that mentees value the professional help and networking experience the most. IISE can plan events that surround these topics in order to increase member satisfaction. Contrastingly, from the most negative results, we see that most mentees disliked the time commitment the program takes. IISE can use this feedback to plan to reduce other time commitments required from members.
Furthermore, we used word clouds to visualize the most used words in each set of positive words and negative words to gain a big picture overview of what members commented. The positive words include words such as “professional” and “network” as well as strong adjectives such as “definitely,” “great,” and “loved.” The negative word cloud contains more mediocre words, such as “kinda,” “less,” and “disliked.”
 
Query 2: How skilled should a mentee expect to be at the end of the mentorship program? What factors contribute most to a higher proficiency level?
Another factor which is important in evaluating the effectiveness of the mentorship program is the improvement in mentees’ skills. The proficiency levels (1-5) are to be tracked for each mentee at the start and end of the mentorship semester; proficiency levels of mentors are also to be tracked. These factors are then evaluated together to provide a holistic view of student performance through an academic year with the mentorship program. Our team built a SQL query that aggregates mentee skills, each skill proficiency level at the start and the end, and mentor proficiency. We also queried some other features that we think might be interesting and important to show a mentee’s improvement, mentor ratings, mentee ratings, alumni year of involvement, and student graduation year. We aggregated everything and loaded the table into a Jupyter notebook to analyze it.
We used a linear regression model to predict a mentee's proficiency level at the end of the program. Our primary method of feature selection was by using the p-value. We started with nine features and selected a feature by its p-value. If the p-value of a feature is more significant than 0.05, we reject the null hypothesis because the feature is insignificant. We only keep features in the 95% confidence interval. At the end of our feature selection, we have the two most important features: mentor proficiency and mentee start proficiency. This model has the R-squared value of 0.75, the adjusted R-squared is 0.732, and the out-of-sample R-squared is 0.67. Overall the average mentee proficiency increased from 1.0 at the beginning of the program to 2.375 at the end of the program.
Interpreting the result, we see that the R-squared and the adjusted R-squared do not differ significantly, which means the features that we selected improve that model as expected. From the model, mentor proficiency is the essential factor in enhancing a mentee's proficiency, and there is a positive correlation between mentor proficiency and mentee end proficiency. Hence, IISE should focus on providing more training to mentors to help their mentee's performance. A mentee should expect their skill proficiency to increase by at least 137.5%.
After obtaining the linear regression model, we aimed to determine which mentors are best at passing down their skills to their mentees. We did so by ranking mentors in terms of their average mentee improvement. Using our fake data, the best mentor is Seth Carson, whose average mentee improvement is 2.

## Further Works
Looking ahead, a huge milestone for IISE would be to use the information stored in this database to better match mentorship pairings based on mutual interests, skills, experiences, and personalities. With more time, we plan to implement the stable marriage algorithm between potential mentors and mentees to optimize the overall pairing preferences. This model would require information on mentor/mentee preferences from all mentor and mentee participants in order to perform correctly. The goal of using this algorithm as opposed to manual matching is to better match pairings and increase mentorship effectiveness and satisfaction. There are, however, benefits and drawbacks from using either manual matching or the stable marriage algorithm. With respect to manual matching, the club team members are able to holistically see the mentors and mentees, skip certain people, and adjust pairings as needed. The downside of this would be that, as the program grows, this method would be more time consuming/less efficient with respect to time. With respect to the stable marriage algorithm, as the mentorship program grows, the algorithm would be more efficient compared to manual matching. One potential drawback of using the stable marriage algorithm is that the algorithm can be unfair, since a list of people in random order is used, and those who are later in the list are less likely to be matched with their preferences. Furthermore, it would be useful for us to initiate a pilot study with a small subset of the data we suggested that the IISE collect in order to further evaluate our proposed database, queries, and validate its effectiveness and use for the IISE.
