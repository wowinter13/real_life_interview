
**Return user_ids with email duplicates**

```sql
SELECT id, email
FROM users
WHERE email IN (
  SELECT email
  FROM users
  GROUP BY email
  HAVING COUNT(*) > 1
);
```

**Return related people where users have email duplicates**

```sql

SELECT u.id, u.email, p.*
FROM users u
JOIN persons p ON u.id = p.user_id
WHERE u.email IN (
  SELECT email
  FROM users
  GROUP BY email
  HAVING COUNT(*) > 1
);

```