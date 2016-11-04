## Will outline best practices and pitfalls to avoid when using middleware

middleware should not breach the routes down into business logic.
the 'req' and 'res' objects should never get passed down beyond the routers
middleware should not 'decorate' objects that get passed down into the business logic (however decorating the 'req' or 'res' is ok, then passing down individual objects from 'req' or 'res' to the business logic is ok)