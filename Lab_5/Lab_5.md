# Lab 5: SQL Continued

Jake Dame  
21 Jun 2024  
CS 6016: Database Systems & Applications  
Dr. Nabil Makarem  

## "Find the name of the patron who has checked out the most books."

```SQL
select p.Name
from Patrons p
natural join
(select c.CardNum, count(*) as CheckoutCount
from CheckedOut c
group by c.CardNum
order by CheckoutCount desc limit 1) as Readers;
```

## "Find the Titles of all books that were written by an author whose name starts with 'K'. You can assume author names always start with an uppercase letter"

```SQL
select t.Title
from Titles t
where t.Author like 'K%';
```

## "Find the Authors who have written more than one book. Assume that two Authors with the same name are the same Author for this query."

```SQL
select t.Author
from Titles t
group by t.Author having count(*) > 1;
```

## "Find the Authors for which the library has more than one book in inventory (this includes multiple copies of the same book). Assume that two Authors with the same name are the same Author for this query."

```SQL
select Author
from Titles
natural join
(select ISBN, count(*) as Books
from Inventory
group by ISBN having Books > 1;
) as MultipleCopies;
```

## "The library wants to implement a customer loyalty program based on how many books each patron has checked out. Provide an SQL query that returns the names, number of books they have checked out, and loyalty level of each Patron. The loyalty level should be the string "Platinum" if they have checked out > 2 books, "Gold" if they have 2 books, "Silver" if they have 1 book, and "Bronze" if they have no books. Hint: remember that NULL represents an unknown in SQL (it does not represent 0)."

```SQL
select Name, Books,
	case
		when Books > 2 then 'Platinum'
		when Books = 2 then 'Gold'
		when Books = 1 then 'Silver'
		else 'Bronze'
	end as Levels
from
	(select p.Name, count(c.CardNum) as Books
	from CheckedOut c right join Patrons p on p.CardNum = c.CardNum
	group by p.CardNum
	) as BooksPerPatron
order by Books desc;
```
