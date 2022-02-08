# Articles, various links and resources

### Is repository pattern useful in EF?
https://www.thereformedprogrammer.net/is-the-repository-pattern-useful-with-entity-framework-core/#:~:text=No%2C%20the%20repository%2Funit%2D,EF%20Core%20isn't%20helpful.

tldr
`No, the repository/unit-of-work pattern (shortened to Rep/UoW) isn’t useful with EF Core. EF Core already implements a Rep/UoW pattern, so layering another Rep/UoW pattern on top of EF Core isn’t helpful.

A better solution is to use EF Core directly, which allows you to use all of EF Core’s feature to produce high-performing database accesses.`

Look at Option 2 – DDD-styled entity classes
access can be modified with EF backing fields.

Related DDD concept: aggregate
aggregate is a cluster of domain objects treated as single unit. for example listings in order or songs in playlist.
aggregate should be accessed only through aggreggate root.