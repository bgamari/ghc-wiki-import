### Comparison between plan MS and MC


#### Plan MC (type functions in GHC)


- local assumptions are turned into rewrite rules

- "simple", easily fits into GHC's current scheme

- but has some restrictions, eg.

- (local) equations must be oriented

- (local) equaitons must be terminating and confluent

- only during constraint generation we may be able to test whether conditions are satisfied

#### Plan MS


- maps the problem to CHRs

- more complete, less restrictive but may require more substantial changes to GHC's inference engine
