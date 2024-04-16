---
marp: true
---

# Data Annotations

- Key ([Key]): Primary key/ Indentity. Can be identified by property Id or ClassName + Id
- Composite key: Multiple [Key]
```cs
[Key]
[Column(Order=1)]
public int PassportNumber { get; set; }
[Key]
[Column(Order = 2)]
public string IssuingCountry { get; set; }
```
- Required [Required]: Client side validation, not allowing the field to be empty
- MaxLength [MaxLength], MinLength [MinLength]: Allow size check for strings
```cs
[MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```
---
# Data Annontations
- NotMapped [NotMapped]: Property won't be mapped into the database
```cs
[NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```
---
# Data Anonntations
- ConcurrencyCheck [ConcurrencyCheck]: 
1. SaveChanges 
2. Update using updated fields + key in WHERE statement 
3. Check if any of updated fields changed in the process 
4. throw DbUpdateConcurrencyException if true
```cs
[ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```
```sql
where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```
---
# Data Annontations

- Complex Type [ComplexType]: For composite entities/ weak entities
```cs
[ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
public class Blog {
    ...
    public BlogDetails BlogDetail { get; set; }
}
```
![](https://learn.microsoft.com/en-us/ef/ef6/media/jj591583-figure06.png)

---
# Data Annontations
- Table [Table]: Custom table name
```cs
[Table("InternalBlogs")]
    public class Blog
```
- Columns [Column]: Custom column name
```cs
[Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```
- DatabaseGenerated [DatabaseGenerated]: Computed columns by the db. Other enums are None and Identity
```cs
[DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```
--- 
# Data Annontations
- Index [Index]: Create index on the column, can use IsUnique
```cs
public int UserId { get; set; }
[Index(IsUnique = true)]
[StringLength(200)]
public string Username { get; set; }
public string DisplayName { get; set; }
```
- Multiple columns indexes:
```cs
public int Id { get; set; }
public string Title { get; set; }
public string Content { get; set; }
[Index("IX_BlogIdAndRating", 2)]
public int Rating { get; set; }
[Index("IX_BlogIdAndRating", 1)]
public int BlogId { get; set; }
```
---

# Entity Relationships
```cs
public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        [ForeignKey("BlogId")]
        public Blog Blog { get; set; }
        public ICollection<Comment> Comments { get; set; }
        public Person CreatedBy { get; set; }
        public Person UpdatedBy { get; set; }
    }
public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```
---
# Entity Relationships
![](https://learn.microsoft.com/en-us/ef/ef6/media/jj591583-figure10.png)

---
# Entity Relationships
```cs
public clas Person{
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
}
```
![](https://learn.microsoft.com/en-us/ef/ef6/media/jj591583-figure11.png)

---
# Code First Conventions
## Type discovery
- Derived class from DbContext
- Properties as DbSet<EntityClass>
## Primary key Convention
- ID fields: Id / ClassNameId ( DepartmentId, DepartmentID )
---
## Relationship Convention
Implicit foregin key mapping, any direction data manipulation
```cs
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}
```

---
