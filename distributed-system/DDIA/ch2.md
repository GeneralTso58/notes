find the right data model abstraction to hide the complexity of lower layers and enable creation on top of it
## relational DB
- support many to one and many to many relationships
    - many to one relationship
        - many employees can be in the same department. an employee cannot be in the same department
        - design: The "many" side of the relationship contains a foreign key column that references the primary key column of the "one" side.
        - query: SQL join
        - normalization: used for storing human-meaningful data at a centralized place. reduce duplication. easier to maintain and kept consistent
    - many to many:
        - one student can take many classes and one class can have many students
        - design: if A and B has many to many relationship, create a join table that has A and B's primary keys as two foreign keys.
        - query: SQL join three tables
    - table join:
        - we create cartesian products of rows that have matching condition: if A's a,b,c matches condition with B's c,d,e rows, the joined table has ac, ad, ae, bc, bd, be, ... etc.
        - inner joins, full joins defines what to do when one table does not have rows matching the join condition. 
- query optimizers: decides the strategy (access path) to execute queries. This has somehow become a good abstraction that answers all use cases and only need to be built once. **what made the programming model a good abstraction?** 

## noSQL
- more scalable
- specialized query ops
- restrictiveness of relational schema
- goes well with OOP object model and allows better model locality (when you need to load the entire document at the same time)
- keep it small (queries need to load the entire doc) and avoid writes that increase the size of document

schema on read is similar to dynamic type checking and schema on write is like static type checking

schema update might be a pain for the more inflexible relational DB when data volume is big

## Query language
- imperative language tells the computer to execute operations in certain order
- declarative language, you specify the data you want but not how to get it.
    - it is usually more limited in functionality (e.g. you don't specify order). this allows DB to automatically optimize
    - bc you don't specify execution order, we can parallelize execution
    - In HTML and CSS, you also just specify the behavior and let the browser to figure out how to achieve it. This usually results in better performance than JS imperative code. (This is also how configuration-based system works)
- mixture of two
    - map reduce querying
        - map reduce framework and filters are declarative. i.e. we do not know how the underlying system chooses to  carry out map reduce
        - the map and reduce functions are imperative
        - this approach allows for a bit more expressivity of map and reduce function and trade this off with the benefit of query auto-optimization

