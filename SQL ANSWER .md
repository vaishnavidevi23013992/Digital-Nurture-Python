# ANSI SQL Using MySQL Exercises Answers

**Prepared By:** VAISHNAVIDEVI V

**College:** Saveetha Engineering College  

**Department:** B.E CSE – III Year

---

# 1. User Upcoming Events

## Question
Show a list of all upcoming events a user is registered for in their city, sorted by date.

## Answer

```sql
SELECT u.full_name,
       e.title,
       e.city,
       e.start_date
FROM Users u
JOIN Registrations r ON u.user_id = r.user_id
JOIN Events e ON r.event_id = e.event_id
WHERE e.status = 'upcoming'
  AND u.city = e.city
ORDER BY e.start_date;
```
# Output
<img width="1608" height="184" alt="image" src="https://github.com/user-attachments/assets/24840fad-1d8b-43d2-b627-825373563e32" />


# 2. Top Rated Events

## Question
Identify events with the highest average rating, considering only those that have received at least 10 feedback submissions.

## Answer

```sql
SELECT e.event_id,
       e.title,
       AVG(f.rating) AS avg_rating,
       COUNT(*) AS feedback_count
FROM Events e
JOIN Feedback f ON e.event_id = f.event_id
GROUP BY e.event_id, e.title
HAVING COUNT(*) >= 10
ORDER BY avg_rating DESC;
```
# Output
<img width="1608" height="114" alt="image" src="https://github.com/user-attachments/assets/996968da-264a-4ba1-83b2-ce803ad5fb1d" />

---

# 3. Inactive Users

## Question
Retrieve users who have not registered for any events in the last 90 days.

## Answer

```sql
SELECT *
FROM Users
WHERE user_id NOT IN (
    SELECT DISTINCT user_id
    FROM Registrations
    WHERE registration_date >= CURDATE() - INTERVAL 90 DAY
);
```
# Output
<img width="1891" height="346" alt="image" src="https://github.com/user-attachments/assets/31f0880b-3c75-493a-b431-b7893f6b890a" />

---

# 4. Peak Session Hours

## Question
Count how many sessions are scheduled between 10 AM to 12 PM for each event.

## Answer

```sql
SELECT e.event_id,
       e.title,
       COUNT(s.session_id) AS session_count
FROM Events e
LEFT JOIN Sessions s
ON e.event_id = s.event_id
AND TIME(s.start_time) BETWEEN '10:00:00' AND '12:00:00'
GROUP BY e.event_id, e.title;
```
# Output
<img width="1606" height="237" alt="image" src="https://github.com/user-attachments/assets/750a6068-1a12-4f59-b432-07ca88c6ed02" />

---

# 5. Most Active Cities

## Question
List the top 5 cities with the highest number of distinct user registrations.

## Answer

```sql
SELECT u.city,
       COUNT(DISTINCT r.registration_id) AS total_registrations
FROM Users u
JOIN Registrations r ON u.user_id = r.user_id
GROUP BY u.city
ORDER BY total_registrations DESC
LIMIT 5;
```
# Output
<img width="1606" height="234" alt="image" src="https://github.com/user-attachments/assets/1391aea4-50cd-47a0-8e9b-64df3a2959e9" />

---
# 6. Event Resource Summary

## Question

Generate a report showing the number of resources (PDFs, Images, Links) uploaded for each event.

## Answer

```sql
SELECT e.event_id,
       e.title,
       COUNT(CASE WHEN r.resource_type='pdf' THEN 1 END) AS pdf_count,
       COUNT(CASE WHEN r.resource_type='image' THEN 1 END) AS image_count,
       COUNT(CASE WHEN r.resource_type='link' THEN 1 END) AS link_count
FROM Events e
LEFT JOIN Resources r ON e.event_id = r.event_id
GROUP BY e.event_id, e.title;
```
# Output
<img width="1609" height="238" alt="image" src="https://github.com/user-attachments/assets/a30e3e8b-d748-45fc-9531-c5c8504a1693" />

---

# 7. Low Feedback Alerts

## Question

List all users who gave feedback with a rating less than 3, along with their comments and associated event names.

## Answer

```sql
SELECT u.full_name,
       e.title AS event_name,
       f.rating,
       f.comments
FROM Feedback f
JOIN Users u ON f.user_id = u.user_id
JOIN Events e ON f.event_id = e.event_id
WHERE f.rating < 3;
```
# Output
<img width="1610" height="102" alt="image" src="https://github.com/user-attachments/assets/6deea930-b43e-40e9-be1f-63607d2249f9" />

---

# 8. Sessions per Upcoming Event

## Question

Display all upcoming events with the count of sessions scheduled for them.

## Answer

```sql
SELECT e.event_id,
       e.title,
       COUNT(s.session_id) AS session_count
FROM Events e
LEFT JOIN Sessions s ON e.event_id = s.event_id
WHERE e.status = 'upcoming'
GROUP BY e.event_id, e.title;
```
# Output
<img width="1615" height="186" alt="image" src="https://github.com/user-attachments/assets/5098694c-9259-427d-a8e1-a96e20910c26" />

---

# 9. Organizer Event Summary

## Question

For each event organizer, show the number of events created and their current status.

## Answer

```sql
SELECT u.user_id,
       u.full_name,
       e.status,
       COUNT(e.event_id) AS total_events
FROM Users u
JOIN Events e ON u.user_id = e.organizer_id
GROUP BY u.user_id, u.full_name, e.status;
```
# Output
<img width="1614" height="239" alt="image" src="https://github.com/user-attachments/assets/e6614c11-17c5-47c4-8db3-7fd50986ba22" />

---

# 10. Feedback Gap

## Question

Identify events that had registrations but received no feedback at all.

## Answer

```sql
SELECT e.event_id,
       e.title
FROM Events e
WHERE EXISTS (
      SELECT 1
      FROM Registrations r
      WHERE r.event_id = e.event_id
)
AND NOT EXISTS (
      SELECT 1
      FROM Feedback f
      WHERE f.event_id = e.event_id
);
```
# Output
<img width="1616" height="142" alt="image" src="https://github.com/user-attachments/assets/3b9affb7-d988-447e-ad4a-729c303423a6" />

---

# 11. Daily New User Count

## Question

Find the number of users who registered each day in the last 7 days.

## Answer

```sql
SELECT registration_date,
       COUNT(*) AS new_users
FROM Users
WHERE registration_date >= CURDATE() - INTERVAL 7 DAY
GROUP BY registration_date
ORDER BY registration_date;
```
# Output
<img width="1615" height="98" alt="image" src="https://github.com/user-attachments/assets/9828d8e6-d1f2-4a00-943b-b57f0a02ef7f" />

---

# 12. Event with Maximum Sessions

## Question

List the event(s) with the highest number of sessions.

## Answer

```sql
SELECT e.event_id,
       e.title,
       COUNT(s.session_id) AS session_count
FROM Events e
JOIN Sessions s ON e.event_id = s.event_id
GROUP BY e.event_id, e.title
HAVING COUNT(s.session_id) = (
    SELECT MAX(session_total)
    FROM (
        SELECT COUNT(*) AS session_total
        FROM Sessions
        GROUP BY event_id
    ) x
);
```
# Output
<img width="1606" height="134" alt="image" src="https://github.com/user-attachments/assets/55f57354-d7fe-4239-9b3c-140ec7f98636" />

---

# 13. Average Rating per City

## Question

Calculate the average feedback rating of events conducted in each city.

## Answer

```sql
SELECT e.city,
       ROUND(AVG(f.rating),2) AS avg_rating
FROM Events e
JOIN Feedback f ON e.event_id = f.event_id
GROUP BY e.city;
```
# Output
<img width="1617" height="185" alt="image" src="https://github.com/user-attachments/assets/0e04514c-42d1-4cf8-bd29-d155663a1654" />

---

# 14. Most Registered Events

## Question

List top 3 events based on the total number of user registrations.

## Answer

```sql
SELECT e.event_id,
       e.title,
       COUNT(r.registration_id) AS total_registrations
FROM Events e
JOIN Registrations r ON e.event_id = r.event_id
GROUP BY e.event_id, e.title
ORDER BY total_registrations DESC
LIMIT 3;
```
# Output
<img width="1610" height="239" alt="image" src="https://github.com/user-attachments/assets/1931e26c-a03d-40eb-95bf-99d2a6edc74d" />

---

# 15. Event Session Time Conflict

## Question

Identify overlapping sessions within the same event.

## Answer

```sql
SELECT s1.event_id,
       s1.session_id AS session_1,
       s2.session_id AS session_2
FROM Sessions s1
JOIN Sessions s2
ON s1.event_id = s2.event_id
AND s1.session_id < s2.session_id
AND s1.start_time < s2.end_time
AND s1.end_time > s2.start_time;
```
# Output
<img width="1622" height="107" alt="image" src="https://github.com/user-attachments/assets/484c1dd8-e824-47c9-9aa5-e250f5641759" />

---
# 16. Unregistered Active Users

## Question

Find users who created an account in the last 30 days but haven’t registered for any events.

## Answer

```sql
SELECT u.*
FROM Users u
LEFT JOIN Registrations r ON u.user_id = r.user_id
WHERE u.registration_date >= CURDATE() - INTERVAL 30 DAY
AND r.registration_id IS NULL;
```
# Output
<img width="1617" height="107" alt="image" src="https://github.com/user-attachments/assets/bdc200da-c26d-4756-bc67-efadd3d7bf7c" />

---

# 17. Multi-Session Speakers

## Question

Identify speakers who are handling more than one session across all events.

## Answer

```sql
SELECT speaker_name,
       COUNT(*) AS total_sessions
FROM Sessions
GROUP BY speaker_name
HAVING COUNT(*) > 1;
```
# Output
<img width="1616" height="115" alt="image" src="https://github.com/user-attachments/assets/8fc0cb14-393f-41be-b9d7-27dd46385a88" />

---

# 18. Resource Availability Check

## Question

List all events that do not have any resources uploaded.

## Answer

```sql
SELECT e.event_id,
       e.title
FROM Events e
LEFT JOIN Resources r ON e.event_id = r.event_id
WHERE r.resource_id IS NULL;
```
# Output
<img width="1618" height="115" alt="image" src="https://github.com/user-attachments/assets/a0ebc458-9a9b-47c8-981f-aea41415b635" />

---

# 19. Completed Events with Feedback Summary

## Question

For completed events, show total registrations and average feedback rating.

## Answer

```sql
SELECT e.event_id,
       e.title,
       COUNT(DISTINCT r.registration_id) AS total_registrations,
       ROUND(AVG(f.rating),2) AS avg_rating
FROM Events e
LEFT JOIN Registrations r ON e.event_id = r.event_id
LEFT JOIN Feedback f ON e.event_id = f.event_id
WHERE e.status = 'completed'
GROUP BY e.event_id, e.title;
```
# Output
<img width="1612" height="132" alt="image" src="https://github.com/user-attachments/assets/39fdf336-6feb-4d25-871d-446fe37cb710" />

---

# 20. User Engagement Index

## Question

For each user, calculate how many events they attended and how many feedbacks they submitted.

## Answer

```sql
SELECT u.user_id,
       u.full_name,
       COUNT(DISTINCT r.event_id) AS attended_events,
       COUNT(DISTINCT f.feedback_id) AS feedback_submitted
FROM Users u
LEFT JOIN Registrations r ON u.user_id = r.user_id
LEFT JOIN Feedback f ON u.user_id = f.user_id
GROUP BY u.user_id, u.full_name;
```
# Output
<img width="1900" height="355" alt="image" src="https://github.com/user-attachments/assets/84ac23df-5c42-4f7c-a846-b38d816bcb1a" />

---

# 21. Top Feedback Providers

## Question

List top 5 users who have submitted the most feedback entries.

## Answer

```sql
SELECT u.user_id,
       u.full_name,
       COUNT(f.feedback_id) AS total_feedbacks
FROM Users u
JOIN Feedback f ON u.user_id = f.user_id
GROUP BY u.user_id, u.full_name
ORDER BY total_feedbacks DESC
LIMIT 5;
```
# Output
<img width="1611" height="237" alt="image" src="https://github.com/user-attachments/assets/8815933d-41b3-431a-a016-c91245509b16" />

---

# 22. Duplicate Registrations Check

## Question

Detect if a user has been registered more than once for the same event.

## Answer

```sql
SELECT user_id,
       event_id,
       COUNT(*) AS registration_count
FROM Registrations
GROUP BY user_id, event_id
HAVING COUNT(*) > 1;
```
# Output
<img width="1603" height="113" alt="image" src="https://github.com/user-attachments/assets/7bce5c31-94f3-4f7b-9757-c4eb20ebf8fb" />

---

# 23. Registration Trends

## Question

Show a month-wise registration count trend over the past 12 months.

## Answer

```sql
SELECT DATE_FORMAT(registration_date,'%Y-%m') AS month,
       COUNT(*) AS registration_count
FROM Registrations
WHERE registration_date >= CURDATE() - INTERVAL 12 MONTH
GROUP BY DATE_FORMAT(registration_date,'%Y-%m')
ORDER BY month;
```
# Output
<img width="1619" height="128" alt="image" src="https://github.com/user-attachments/assets/1ce64e8b-9f08-48e0-99b2-6d1258f07fb9" />

---

# 24. Average Session Duration per Event

## Question

Compute the average duration (in minutes) of sessions in each event.

## Answer

```sql
SELECT e.event_id,
       e.title,
       ROUND(
           AVG(
               TIMESTAMPDIFF(
                   MINUTE,
                   s.start_time,
                   s.end_time
               )
           ),2
       ) AS avg_duration_minutes
FROM Events e
JOIN Sessions s ON e.event_id = s.event_id
GROUP BY e.event_id, e.title;
```
# Output
<img width="1618" height="238" alt="image" src="https://github.com/user-attachments/assets/e8097de7-a236-49e0-93fd-c602f7c4ffc9" />

---

# 25. Events Without Sessions

## Question

List all events that currently have no sessions scheduled under them.

## Answer

```sql
SELECT e.event_id,
       e.title
FROM Events e
LEFT JOIN Sessions s ON e.event_id = s.event_id
WHERE s.session_id IS NULL;
```
# Output
<img width="1612" height="115" alt="image" src="https://github.com/user-attachments/assets/bde2d587-adf0-47fa-a939-5a06f82ffae4" />

---

