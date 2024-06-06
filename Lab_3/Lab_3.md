# Lab 3: Relational Algebra

Jake Dame  
06 Jun 2024  
CS 6016: Database Systems & Applications  
Dr. Nabil Makarem  

# Part 1: Joins

T1
| A   | Q   | R   |
| :-- | :-- | :-- |
| 20  | a   | 5   |
| 25  | b   | 8   |
| 35  | a   | 6   |

T2
| A   | B   | C   |
| :-- | :-- | :-- |
| 20  | b   | 6   |
| 45  | c   | 3   |
| 20  | b   | 5   |

"Provide the relations that result from the following queries. Your relations should be in the form of a table, and should include the schema."

___

**1. $T1 \bowtie_{T1.A=T2.A} T2$**

| A   | Q   | R   | A   | B   | C   |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 20  | a   | 5   | 20  | b   | 6   |
| 20  | a   | 5   | 20  | b   | 5   |

___

**2. $T1 \bowtie_{T1.Q=T2.B} T2$**

| A   | Q   | B   | R   | C   |
| :-- | :-- | :-- | :-- | :-- |
| 25  | b   | b   | 8   | 6   |
| 25  | b   | b   | 8   | 5   |

___

**3. $T1 \bowtie T2$**

*Note: This is similar to the join in "1." by virtue of the fact that column A is the common column between both tables anyway; however, because it is a natural join, duplicate columns are not retained*

| A   | Q   | R   | B   | C   |
| :-- | :-- | :-- | :-- | :-- |
| 20  | a   | 5   | b   | 6   |
| 20  | a   | 5   | b   | 5   |

___

**4. $T1 \bowtie_{T1.A = T2.A \land T1.R = T2.C} T2$**

| A   | Q   | R   | A   | B   | C   |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 20  | a   | 5   | 20  | b   | 5   |

# Part 2: Chess Queries

Events
| Name               | Year | eID |
| :----------------- | :--- | :-- |
| World Championship | 1987 | 1   |
| Moscow Open        | 2018 | 2   |
| World Championship | 2018 | 3   |

Players
| Name            | Elo  | pID |
| :-------------- | :--- | :-- |
| Magnus Carlsen  | 2882 | 1   |
| Judit Polgar    | 2735 | 2   |
| Fabiano Caruana | 2844 | 3   |
| Gary Kasparov   | 2851 | 4   |
| Anatoly Karpov  | 2780 | 5   |

Games
| gID | eID | Result  | wpID | bpID |
| :-- | :-- | :------ | :--- | :--- |
| 1   | 3   | 1/2-1/2 | 1    | 3    |
| 2   | 3   | 1/2-1/2 | 3    | 1    |
| 3   | 2   | 1-0     | 2    | 1    |
| 4   | 1   | 1/2-1/2 | 4    | 5    |
| 5   | 3   | 0-1     | 3    | 1    |

"Write relational algebra queries for the following. You can (and should) write your query on multiple lines if you use the renaming operator."

___

**1. "Find the names of any player with an Elo rating of 2850 or higher."**
- $\pi_{Name}(\sigma_{Elo >= 2850}(Players))$

**2. "Find the names of any player who has ever played a game as white."**
- $\pi_{Name}(Games \bowtie_{Games.wpID = Players.pID} Players)$

**3. "Find the names of any player who has ever won a game as white."**
- $\pi_{Name}((\sigma_{Result = 1-0}(Games) \bowtie_{Games.wpID = Players.pID} Players))$

**4. "Find the names of any player who played any games in 2018."**
- $Players2018 = (\sigma_{Year = 2018}(Events) \bowtie Games)$
- $\pi_{Name}( Players2018 \bowtie_{Games.wpID=Players.pID \lor Games.bpID=Players.pID})$

**5. "Find the names and dates of any event in which Magnus Carlsen lost a game."**
- Find Magnus Carlsen's pID (1)
- Find any game where the Result is 1-0 and the bpID is 1 or where the Result is 0-1 and the wpID is 1

**6. "Find the names of all opponents of Magnus Carlsen."**
- Find Magnus Carlsen's pID (1)
- Find all games where wpID or bpID is 1
- 

# Part 3: LMS Queries

Students
| sID | Name     | DOB  |
| :-- | :------- | :--- |
| 1   | Hermione | 1980 |
| 2   | Harry    | 1979 |
| 3   | Ron      | 1980 |
| 4   | Malfoy   | 1982 |

Enrolled
| sID | cID  | Grd |
| :-- | :--- | :-- |
| 1   | 3500 | A   |
| 1   | 3810 | A-  |
| 1   | 5530 | A   |
| 2   | 3810 | A   |
| 2   | 5530 | B   |
| 3   | 3500 | C   |
| 3   | 3810 | B   |
| 4   | 3500 | C   |

Courses
| cID  | Name         |
| :--- | :----------- |
| 3500 | SW Practice  |
| 3810 | Architecture |
| 5530 | Databases    |

"\[Provide English descriptions of the query and the resulting relation based on the tables above.\]"

___

**3.1**

"$\rho(C, \pi_{sID}(\sigma_{Grd = C}(Enrolled)))$"
- Select students from  Enrolled whose Grade was C; project their sID; put those students into a new table named "C"
- "Create a new table called "C" with the IDs of enrolled students who got a C."

"$\pi_{Name}((\pi_{sID}(Enrolled) - C) \bowtie Students)$"
- Get the sIDs of students in Enrolled; out of those, find the ones who are not in the "C" table; match the resulting students to the ones in Students; project the names of the resulting students
- "Get the names of enrolled students who never got a C."

**Result:**

| Name     |
| :------- |
| Hermione |
| Harry    |

___

**3.2**

$\rho(S1, Students)$
- Create a table named "S1" from the contents of "Students"

$\rho(S2, Students)$
- Create a table named "S2" from the contents of "Students"

$\pi_{S2.Name}(\sigma_{S1.Name=Ron \land S1.DOB=S2.DOB \land S2.Name != Ron}(S1 \times S2))$
- Get every possible combination of student; select the rows where the birthdays match, but do not allow Ron to be matched with his "alias"; get the Name values of the resulting students
- "Find students with matching birth years."

**Result:**

| S2.Name  |
| :------- |
| Hermione |

___

**3.3**

"$\pi_{Name}(( \pi_{cID,sID}(Enrolled) / \pi_{sID}(Students) ) \bowtie Courses)$"
- Get the course and student IDs from Enrolled, and divide out by all students; match the resulting course ID values with the course IDs in Courses; project the names of the resulting courses

**Result:**

| cID |
| :-- |

___

**3.4**

"Provide a relational algebra query that uses the divide operator to find the names of all students who are taking all of the 3xxx-level classes."

Get the classes we are interested in
- $C = \pi_{cID >= 3000 \land cID< 4000} (Courses)$

Find tuples in Courses.cID that have all values of C.cID
- $S = \pi_{sID}(Courses/C)$

Join; project the students' names
- $\pi_{Names}(S \bowtie Students)$