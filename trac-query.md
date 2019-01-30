# Trac Ticket Queries






In addition to [reports](trac-reports), Trac provides support for *custom ticket queries*, used to display lists of tickets meeting a specified set of criteria. 



To configure and execute a custom query, switch to the *View Tickets* module from the navigation bar, and select the *Custom Query* link.


## Filters



When you first go to the query page the default filter will display tickets relevant to you:


- If logged in then all open tickets it will display open tickets assigned to you.
- If not logged in but you have specified a name or email address in the preferences then it will display all open tickets where your email (or name if email not defined) is in the CC list.
- If not logged and no name/email defined in the preferences then all open issues are displayed.


Current filters can be removed by clicking the button to the left with the minus sign on the label.  New filters are added from the pulldown lists at the bottom corners of the filters box ('And' conditions on the left, 'Or' conditions on the right).  Filters with either a text box or a pulldown menu of options can be added multiple times to perform an *or* of the criteria.



You can use the fields just below the filters box to group the results based on a field, or display the full description for each ticket.



Once you've edited your filters click the *Update* button to refresh your results.


## Navigating Tickets



Clicking on one of the query results will take you to that ticket.  You can navigate through the results by clicking the *Next Ticket* or *Previous Ticket* links just below the main menu bar, or click the *Back to Query* link to return to the query page.  



You can safely edit any of the tickets and continue to navigate through the results using the *Next/Previous/Back to Query* links after saving your results.  When you return to the query *any tickets which were edited* will be displayed with italicized text.  If one of the tickets was edited such that it no longer matches the query criteria  the text will also be greyed. Lastly, if **a new ticket matching the query criteria has been created**, it will be shown in bold. 



The query results can be refreshed and cleared of these status indicators by clicking the *Update* button again.


## Saving Queries



Trac allows you to save the query as a named query accessible from the reports module. To save a query ensure that you have *Updated* the view and then click the *Save query* button displayed beneath the results.
You can also save references to queries in Wiki content, as described below.



*Note:* one way to easily build queries like the ones below, you can build and test the queries in the Custom report module and when ready - click *Save query*. This will build the query string for you. All you need to do is remove the extra line breaks.



*Note:* you must have the **REPORT\_CREATE** permission in order to save queries to the list of default reports. The *Save query* button will only appear if you are logged in as a user that has been granted this permission. If your account does not have permission to create reports, you can still use the methods below to save a query.


### Using [TracLinks](trac-links)



You may want to save some queries so that you can come back to them later.  You can do this by making a link to the query from any Wiki page.


```wiki
[query:status=new|assigned|reopened&version=1.0 Active tickets against 1.0]
```


Which is displayed as:


>
>
> Active tickets against 1.0 (Ticket query: status: new, status: assigned,
> status: reopened, version: 1.0, order: priority)
>
>


This uses a very simple query language to specify the criteria (see [Query Language](trac-query#query-language)).



Alternatively, you can copy the query string of a query and paste that into the Wiki link, including the leading `?` character:


```wiki
[query:?status=new&status=assigned&status=reopened&group=owner Assigned tickets by owner]
```


Which is displayed as:


>
>
> Assigned tickets by owner (Ticket query: status: new, status: assigned,
> status: reopened, group: owner)
>
>

### Using the `[[TicketQuery]]` Macro



The [
TicketQuery](http://trac.edgewall.org/intertrac/TicketQuery) macro lets you display lists of tickets matching certain criteria anywhere you can use [WikiFormatting](wiki-formatting).



Example:


```wiki
[[TicketQuery(version=0.6|0.7&resolution=duplicate)]]
```


This is displayed as:


>
>
> No results
>
>


Just like the [query: wiki links](trac-query#using-traclinks), the parameter of this macro expects a query string formatted according to the rules of the simple [ticket query language](trac-query#query-language). This also allows displaying the link and description of a single ticket:


```wiki
[[TicketQuery(id=123)]]
```


This is displayed as:


>
>
> <table><tr><th>[\#123](https://gitlab.staging.haskell.org/ghc/ghc/issues/123)</th>
> <td>Unix manual pages not in release bundles</td></tr></table>
>
>
>


A more compact representation without the ticket summaries is also available:


```wiki
[[TicketQuery(version=0.6|0.7&resolution=duplicate, compact)]]
```


This is displayed as:


>
>
> No results
>
>


Finally, if you wish to receive only the number of defects that match the query, use the ``count`` parameter.


```wiki
[[TicketQuery(version=0.6|0.7&resolution=duplicate, count)]]
```


This is displayed as:


>
>
> 0 (Ticket query: version: 0.6, version: 0.7, resolution: duplicate, max: 0,
> order: id)
>
>

### Customizing the *table* format



You can also customize the columns displayed in the table format (*format=table*) by using *col=\<field\>* - you can specify multiple fields and what order they are displayed by placing pipes (`|`) between the columns like below:


```wiki
[[TicketQuery(max=3,status=closed,order=id,desc=1,format=table,col=resolution|summary|owner|reporter)]]
```


This is displayed as:


  

##
    Results (1 - 3 of 11745)
  


  



    
    
      
        1
      
      
    
      
      
        2 (Ticket query: status: closed, max: 3, page: 2, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        3 (Ticket query: status: closed, max: 3, page: 3, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        4 (Ticket query: status: closed, max: 3, page: 4, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        5 (Ticket query: status: closed, max: 3, page: 5, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        6 (Ticket query: status: closed, max: 3, page: 6, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        7 (Ticket query: status: closed, max: 3, page: 7, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        8 (Ticket query: status: closed, max: 3, page: 8, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        9 (Ticket query: status: closed, max: 3, page: 9, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        10 (Ticket query: status: closed, max: 3, page: 10, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        11 (Ticket query: status: closed, max: 3, page: 11, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
    
      → (Ticket query: status: closed, max: 3, page: 2, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
    


  
  
  
    
  
  

<table><tr><td>
      </td>
<th>
        
        Ticket (Ticket query: status: closed, max: 3, col: id, col: resolution,
col: summary, col: owner, col: reporter, order: id)
      </th>
<th>
        
        Resolution (Ticket query: status: closed, max: 3, col: id,
col: resolution, col: summary, col: owner, col: reporter, order: resolution)
      </th>
<th>
        
        Summary (Ticket query: status: closed, max: 3, col: id, col: resolution,
col: summary, col: owner, col: reporter, order: summary)
      </th>
<th>
        
        Owner (Ticket query: status: closed, max: 3, col: id, col: resolution,
col: summary, col: owner, col: reporter, order: owner)
      </th>
<th>
        
        Reporter (Ticket query: status: closed, max: 3, col: id,
col: resolution, col: summary, col: owner, col: reporter, order: reporter)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16229](https://gitlab.staging.haskell.org/ghc/ghc/issues/16229)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      duplicate
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Dump-parsed-ast fails for very large Fractional number constants](https://gitlab.staging.haskell.org/ghc/ghc/issues/16229)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      alanz
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16214](https://gitlab.staging.haskell.org/ghc/ghc/issues/16214)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      fixed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Hadrian prof flavour doesn't build a compiler which you can profile](https://gitlab.staging.haskell.org/ghc/ghc/issues/16214)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      mpickering
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16213](https://gitlab.staging.haskell.org/ghc/ghc/issues/16213)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      invalid
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Unnecessary error](https://gitlab.staging.haskell.org/ghc/ghc/issues/16213)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      pjljvdlaar
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr></table>


  



    
    
      
        1
      
      
    
      
      
        2 (Ticket query: status: closed, max: 3, page: 2, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        3 (Ticket query: status: closed, max: 3, page: 3, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        4 (Ticket query: status: closed, max: 3, page: 4, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        5 (Ticket query: status: closed, max: 3, page: 5, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        6 (Ticket query: status: closed, max: 3, page: 6, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        7 (Ticket query: status: closed, max: 3, page: 7, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        8 (Ticket query: status: closed, max: 3, page: 8, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        9 (Ticket query: status: closed, max: 3, page: 9, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        10 (Ticket query: status: closed, max: 3, page: 10, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
      
      
        11 (Ticket query: status: closed, max: 3, page: 11, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
      
    
    
      → (Ticket query: status: closed, max: 3, page: 2, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
    




#### Full rows



In *table* format you can also have full rows by using *rows=\<field\>* like below:


```wiki
[[TicketQuery(max=3,status=closed,order=id,desc=1,format=table,col=resolution|summary|owner|reporter,rows=description)]]
```


This is displayed as:


  

##
    Results (1 - 3 of 11745)
  


  



    
    
      
        1
      
      
    
      
      
        2 (Ticket query: status: closed, max: 3, page: 2, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        3 (Ticket query: status: closed, max: 3, page: 3, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        4 (Ticket query: status: closed, max: 3, page: 4, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        5 (Ticket query: status: closed, max: 3, page: 5, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        6 (Ticket query: status: closed, max: 3, page: 6, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        7 (Ticket query: status: closed, max: 3, page: 7, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        8 (Ticket query: status: closed, max: 3, page: 8, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        9 (Ticket query: status: closed, max: 3, page: 9, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        10 (Ticket query: status: closed, max: 3, page: 10, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        11 (Ticket query: status: closed, max: 3, page: 11, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
    
      → (Ticket query: status: closed, max: 3, page: 2, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
    


  
  
  
    
  
  

<table><tr><td>
      </td>
<th>
        
        Ticket (Ticket query: status: closed, max: 3, col: id, col: resolution,
col: summary, col: owner, col: reporter, order: id, row: description)
      </th>
<th>
        
        Resolution (Ticket query: status: closed, max: 3, col: id,
col: resolution, col: summary, col: owner, col: reporter, order: resolution,
row: description)
      </th>
<th>
        
        Summary (Ticket query: status: closed, max: 3, col: id, col: resolution,
col: summary, col: owner, col: reporter, order: summary, row: description)
      </th>
<th>
        
        Owner (Ticket query: status: closed, max: 3, col: id, col: resolution,
col: summary, col: owner, col: reporter, order: owner, row: description)
      </th>
<th>
        
        Reporter (Ticket query: status: closed, max: 3, col: id,
col: resolution, col: summary, col: owner, col: reporter, order: reporter,
row: description)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16229](https://gitlab.staging.haskell.org/ghc/ghc/issues/16229)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      duplicate
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Dump-parsed-ast fails for very large Fractional number constants](https://gitlab.staging.haskell.org/ghc/ghc/issues/16229)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      alanz
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                    </td>
<th>Description</th>
<td>
                    
                    </td>
<th>
                      


The existing test file `T15271.hs` has


```
main = do
  print 1e646457008
  print 1e646457009 -- T15271: This incorrectly printed 0.0
  print 1e1555550000 -- This is still infinity
  print 1e1000000000 -- T15271: This incorrectly printed 0.0
```


Trying to run `ghc --dump-parsed-ast` T15271.hs\` fails, using up all available memory.



When dumping the file in ghc-exacptrint, the start looks like this


```wiki
                       ({ tests/examples/ghc88-copied/T15271.hs:2:9-19 }
                        Just (Ann (DP (0,1)) [] [] [((G AnnVal),DP (0,0))] Nothing Nothing)
                        (HsOverLit 
                         (NoExt) 
                         (OverLit 
                          (NoExt) 
                          (HsFractional 
                           (FL 
                            (SourceText "1e646457008") 
                            (False) 
                            (:% 
                             (100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
.....
```


I am not sure if this is an actual problem, but it is worth noting.



                    </th>
<td>
                    
                  </td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16214](https://gitlab.staging.haskell.org/ghc/ghc/issues/16214)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      fixed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Hadrian prof flavour doesn't build a compiler which you can profile](https://gitlab.staging.haskell.org/ghc/ghc/issues/16214)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      mpickering
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                    </td>
<th>Description</th>
<td>
                    
                    </td>
<th>
                      


After building a compiler with the `prof` flavour. I tried to use it with the `-xc` option. 


```wiki
./hadrian/build.sh --flavour=prof
_build/stage1/bin/ghc A.hs +RTS -xc
```


But apparently 


```wiki
ghc: the flag -xc requires the program to be built with -prof
```


So it seems that the `prof` flavour is broken. 



                    </th>
<td>
                    
                  </td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16213](https://gitlab.staging.haskell.org/ghc/ghc/issues/16213)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      invalid
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Unnecessary error](https://gitlab.staging.haskell.org/ghc/ghc/issues/16213)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      pjljvdlaar
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                    </td>
<th>Description</th>
<td>
                    
                    </td>
<th>
                      


I don't know whether this is a bug or a feature request....



When compiling


```wiki
{-# LANGUAGE DataKinds           #-}
{-# LANGUAGE FlexibleContexts    #-}
{-# LANGUAGE GADTs               #-}
{-# LANGUAGE KindSignatures      #-}
{-# LANGUAGE TypeOperators       #-}
{-# LANGUAGE TypeFamilies        #-}
{-# LANGUAGE FlexibleInstances   #-}
module Main where

data Term (b::Bool) where
    Const :: Int -> Term 'True
    Sum  :: 
            --Show (TermList v) => 
            TermList v -> Term 'False

instance Show (Term b) where
    show (Const a) = "(Const " ++ show a ++ ")"
    show (Sum a) = "(Sum " ++ show a ++ ")"

data TermList (xs :: [ Bool ]) where
    TNil :: TermList '[]
    TCons :: Term x -> TermList xs -> TermList (x ': xs)

instance Show (TermList '[]) where
    show TNil = "Nil"

instance Show (TermList xs) => Show (TermList (x ': xs)) where
    show (TCons a b) = "(Cons " ++ show a ++ " " ++ show b ++ ")"

main :: IO ()
main = do
        putStrLn "Hello world!"
```


one gets the error


```wiki
src\Main.hs:37:31: error:
    * Could not deduce (Show (TermList v)) arising from a use of `show'
      from the context: b ~ 'False
        bound by a pattern with constructor:
                   Sum :: forall (v :: [Bool]). TermList v -> Term 'False,
                 in an equation for `show'
        at src\Main.hs:37:11-15
    * In the first argument of `(++)', namely `show a'
      In the second argument of `(++)', namely `show a ++ ")"'
      In the expression: "(Sum " ++ show a ++ ")"
   |
37 |     show (Sum a) = "(Sum " ++ show a ++ ")"
   |                               ^^^^^^
```


Yet, both patterns are matched, i.e. '\[\] and (x ': xs),
so I was surprised the compiler could not figure this out!



By out commenting 'Show (TermList v) =\> ' the code complies fine.



                    </th>
<td>
                    
                  </td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td></tr></table>


  



    
    
      
        1
      
      
    
      
      
        2 (Ticket query: status: closed, max: 3, page: 2, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        3 (Ticket query: status: closed, max: 3, page: 3, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        4 (Ticket query: status: closed, max: 3, page: 4, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        5 (Ticket query: status: closed, max: 3, page: 5, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        6 (Ticket query: status: closed, max: 3, page: 6, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        7 (Ticket query: status: closed, max: 3, page: 7, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        8 (Ticket query: status: closed, max: 3, page: 8, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        9 (Ticket query: status: closed, max: 3, page: 9, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        10 (Ticket query: status: closed, max: 3, page: 10, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
      
      
        11 (Ticket query: status: closed, max: 3, page: 11, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id,
row: description)
      
    
    
      → (Ticket query: status: closed, max: 3, page: 2, col: id,
col: resolution, col: summary, col: owner, col: reporter, desc: 1, order: id)
    




### Query Language



`query:` [TracLinks](trac-links) and the `[[TicketQuery]]` macro both use a mini “query language” for specifying query filters. Basically, the filters are separated by ampersands (`&`). Each filter then consists of the ticket field name, an operator, and one or more values. More than one value are separated by a pipe (`|`), meaning that the filter matches any of the values. To include a literal `&` or `|` in a value, escape the character with a backslash (`\`).



The available operators are:


<table><tr><th> **`=`** </th>
<th> the field content exactly matches one of the values 
</th></tr>
<tr><th> **`~=`** </th>
<th> the field content contains one or more of the values 
</th></tr>
<tr><th> **`^=`** </th>
<th> the field content starts with one of the values 
</th></tr>
<tr><th> **`$=`** </th>
<th> the field content ends with one of the values 
</th></tr></table>



All of these operators can also be negated:


<table><tr><th> **`!=`** </th>
<th> the field content matches none of the values 
</th></tr>
<tr><th> **`!~=`** </th>
<th> the field content does not contain any of the values 
</th></tr>
<tr><th> **`!^=`** </th>
<th> the field content does not start with any of the values 
</th></tr>
<tr><th> **`!$=`** </th>
<th> the field content does not end with any of the values 
</th></tr></table>



The date fields `created` and `modified` can be constrained by using the `=` operator and specifying a value containing two dates separated by two dots (`..`). Either end of the date range can be left empty, meaning that the corresponding end of the range is open. The date parser understands a few natural date specifications like "3 weeks ago", "last month" and "now", as well as Bugzilla-style date specifications like "1d", "2w", "3m" or "4y" for 1 day, 2 weeks, 3 months and 4 years, respectively. Spaces in date specifications can be left out to avoid having to quote the query string. 


<table><tr><th> **`created=2007-01-01..2008-01-01`** </th>
<th> query tickets created in 2007 
</th></tr>
<tr><th> **`created=lastmonth..thismonth`** </th>
<th> query tickets created during the previous month 
</th></tr>
<tr><th> **`modified=1weekago..`** </th>
<th> query tickets that have been modified in the last week 
</th></tr>
<tr><th> **`modified=..30daysago`** </th>
<th> query tickets that have been inactive for the last 30 days 
</th></tr></table>


---



See also: [TracTickets](trac-tickets), [TracReports](trac-reports), [TracGuide](trac-guide)


