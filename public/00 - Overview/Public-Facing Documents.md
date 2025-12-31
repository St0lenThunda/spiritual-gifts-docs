---
audience:
  - individual
  - ministry
  - church
public_category:
  - overview
status: canonical
updated: 2025-12-28T16:43
visibility: public
tags:
  - public/core
---

# 📋 Public-Facing Documents

This note lists all documents marked with `visibility: public`.

---

## All Public Documents

```dataview
TABLE audience, public_category, updated
FROM ""
WHERE visibility = "public"
SORT updated DESC
```

---

## Grouped by Category

### Theology & Discernment
```dataview
LIST
FROM ""
WHERE visibility = "public" AND contains(public_category, "theology")
```

### For Pastors
```dataview
LIST
FROM ""
WHERE visibility = "public" AND contains(public_category, "pastors")
```

### Product
```dataview
LIST
FROM ""
WHERE visibility = "public" AND contains(public_category, "product")
```

### Legal & Ethics
```dataview
LIST
FROM ""
WHERE visibility = "public" AND contains(public_category, "legal")
```

---

## Church-Only View
```dataview
LIST
FROM ""
WHERE visibility = "public" AND contains(audience, "church")
SORT file.name ASC
```

---

## Ministry Admin View
```dataview
LIST
FROM ""
WHERE visibility = "public" AND contains(audience, "ministry")
SORT file.name ASC
```