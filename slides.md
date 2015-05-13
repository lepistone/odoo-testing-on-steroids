layout: false
class: center, middle, inverse

# Title

---
name: agenda

# Agenda

1. Introduction
2. Deep-dive
3. ...

---

# Introduction

---
# Best Practices
---

layout: false
.left-column[
  ## Outdating
]
.right-column[
Data in jobs can become outdated.

No:
```python
@job
def example(session, record_id, vals):
    export(record_id, vals)
```

Yes:
```python
@job
def example(session, record_id):
    export(session.env['model'].browse(record_id))
```

]

---

.left-column[
  ## Outdating
  ## Existence
]

.right-column[
A job can refer to a record which has been deleted.
Always check if it still exists.

No:
```python
@job
def test2(session, record_id):
    export(session.env['model'].browse(record_id))
```

Yes:
```python
@job
def test2(session, record_id):
    record = session.env['model'].browse(record_id)
    if record.exists():
        export(record)
```

]

---

.left-column[
  ## Outdating
  ## Existence
  ## Idempotence
]

.right-column[
A job should, when possible, produce the same result when executed several
times.

No:
```python
@job
def test2(session, record_id):
    export(session.env['model'].browse(record_id))
```

Yes:
```python
@job
def test2(session, record_id):
    record = session.env['model'].browse(record_id)
    if not record.exported:
        export(session.env['model'].browse(record_id))
```

]
