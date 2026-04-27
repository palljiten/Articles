---
layout: post  
title: "Pagination in Backend Systems: Cursor vs Offset"  
date: 2026-04-27 10:00:00 +0530  
categories: [backend, system-design, database]  
tags: [pagination, cursor-pagination, offset-pagination, scalability]  
---

# Pagination in Backend Systems: Cursor vs Offset

Pagination is a fundamental concept in backend systems used to retrieve large datasets in smaller, manageable chunks. While the implementation appears straightforward, the choice of pagination strategy significantly impacts performance and consistency at scale.

This article covers two commonly used approaches: Offset Pagination and Cursor Pagination.

---

## Offset-Based Pagination

Offset pagination retrieves records by skipping a fixed number of rows and returning the next set.

### Query
SELECT * FROM users
ORDER BY created_at DESC
LIMIT 10 OFFSET 20;

### API Pattern
GET /users?page=3&limit=10

### Characteristics
- Simple implementation  
- Supports page-based navigation  
- Suitable for small datasets  

### Limitations

Performance Overhead  
The database processes skipped rows before returning results, which leads to increased query cost for large offsets.

Inconsistent Results  
Insertions or deletions between requests can shift records, leading to duplication or missing data across pages.

Scalability Constraints  
Performance degrades as dataset size grows.

---

## Cursor-Based Pagination

Cursor pagination retrieves records relative to a reference point instead of skipping rows.

### Query
SELECT * FROM users
WHERE created_at < '2026-04-01T10:00:00'
ORDER BY created_at DESC
LIMIT 10;

### API Pattern
GET /users?cursor=encoded_value

### Characteristics
- Uses a reference (cursor) instead of offset  
- Fetches records relative to the last retrieved item  
- Typically implemented using encoded values  

### Advantages

Efficient Query Execution  
Avoids row skipping and leverages indexing effectively.

Consistent Pagination  
New records do not affect previously retrieved pages.

Scalability  
Performs efficiently even with large datasets.

---

## Limitations of Cursor Pagination

- Does not support arbitrary page navigation  
- Requires a stable and indexed sorting field (e.g., id, created_at)  
- Slightly more complex implementation  

---

## Offset vs Cursor Comparison

| Feature        | Offset Pagination      | Cursor Pagination      |
|----------------|------------------------|------------------------|
| Performance    | Degrades with scale    | Consistent and efficient |
| Consistency    | Unstable               | Stable                 |
| Navigation     | Page-based             | Sequential             |
| Complexity     | Low                    | Moderate               |
| Use Case       | Small datasets         | Large-scale systems    |

---

## Use Case Guidance

Offset Pagination
- Administrative dashboards  
- Reporting interfaces  
- Systems requiring page numbers  

Cursor Pagination
- Infinite scroll interfaces  
- High-throughput APIs  
- Real-time data systems  

---

## Example Comparison

Offset-Based Query
SELECT * FROM orders
LIMIT 10 OFFSET 100000;

Cursor-Based Query
SELECT * FROM orders
WHERE id > last_seen_id
LIMIT 10;

---

## Conclusion

Offset pagination offers simplicity but introduces performance and consistency challenges at scale. Cursor pagination, while slightly more complex, provides better efficiency and reliability for large and dynamic datasets.

For systems expected to grow, cursor-based pagination is the preferred approach.
