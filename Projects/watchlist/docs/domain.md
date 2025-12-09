```
ContentItem:
    id: UUID
    userId: foreign key on User.id
    type: MOVIE | SERIES
    title: string
    year: short
    rating: short
    comment: string NULL
    watchedAt: timestamp
    createdAt: timestamp DEFAULT now
```

```
User:
    id: UUID
    login: string
    password: string(md5)
    registeredAt: timestamp DEFAULT now
```