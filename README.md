### 1. Who hosted the world cup?
#### Query
```cypher
MATCH (wc:WorldCup)-[:HOSTED_BY]->(country)

RETURN wc.name, wc.year, country.name

ORDER BY wc.year
```
#### Output

| wc.name                          | wc.year | country.name   |
| -------------------------------- | ------- | -------------- |
| 1930 FIFA World Cup Uruguay      | 1930    | Uruguay        |
| 1934 FIFA World Cup Italy        | 1934    | Italy          |
| 1938 FIFA World Cup France       | 1938    | France         |
| 1950 FIFA World Cup Brazil       | 1950    | Brazil         |
| 1954 FIFA World Cup Switzerland  | 1954    | Switzerland    |
| 1958 FIFA World Cup Sweden       | 1958    | Sweden         |
| 1962 FIFA World Cup Chile        | 1962    | Chile          |
| 1966 FIFA World Cup England      | 1966    | England        |
| 1970 FIFA World Cup Mexico       | 1970    | Mexico         |
| 1974 FIFA World Cup Germany      | 1974    | Germany FR     |
| 1978 FIFA World Cup Argentina    | 1978    | Argentina      |
| 1982 FIFA World Cup Spain        | 1982    | Spain          |
| 1986 FIFA World Cup Mexico       | 1986    | Mexico         |
| 1990 FIFA World Cup Italy        | 1990    | Italy          |
| 1994 FIFA World Cup USA          | 1994    | USA            |
| 1998 FIFA World Cup France       | 1998    | France         |
| 2002 FIFA World Cup Korea        | 2002    | Japan          |
| 2002 FIFA World Cup Korea        | 2002    | Korea Republic |
| 2006 FIFA World Cup Germany      | 2006    | Germany        |
| 2010 FIFA World Cup South Africa | 2010    | South Africa   |
| 2014 FIFA World Cup Brazil       | 2014    | Brazil         |
| 2018 FIFA World Cup Russia       | 2018    | Russia         |
### 2. Top scorers per world cup?
#### Query
```cypher
MATCH (wc:WorldCup)-[:CONTAINS_MATCH]->(m:Match)<-[:IN_MATCH]-(a:Appearance)-[:SCORED_GOAL]->(g:Goal)

MATCH (a)<-[:STARTED|:SUBSTITUTE]-(p:Player)

RETURN 

  wc.year AS WorldCupYear, 

  p.name AS PlayerName, 

  COUNT(g) AS TotalGoals

ORDER BY TotalGoals DESC
```
#### Output
| WorldCupYear | PlayerName                          | TotalGoals |
| ------------ | ----------------------------------- | ---------- |
| 1958         | Just Fontaine                       | 13         |
| 1954         | Sandor Kocsis                       | 11         |
| 1970         | Gerd Mueller                        | 10         |
| 1966         | Eusebio (Eusebio Da Silva Ferreira) | 9          |
| 1950         | Ademir                              | 8          |
| 1930         | Guillermo Stabile                   | 8          |
| 2002         | Ronaldo                             | 8          |
| 1970         | Jairzinho                           | 7          |
| 1974         | Grzegorz Lato                       | 7          |
| 1938         | Leonidas                            | 7          |
### 3. Which countries have never won a match at a World Cup until 2018 version?
#### Query
```cypher
MATCH (c:Country)
WHERE NOT EXISTS {
  MATCH (c)-[:WON]->(:Match)
}
RETURN DISTINCT c.name AS Country
ORDER BY Country;
```
#### Output
Here's the data converted to a markdown table:

| Country                 |

|------------------------|

| Angola                 |

| Bolivia                |

| Canada                 |

| China PR               |

| Dutch East Indies      |

| Egypt                  |

| El Salvador            |

| Haiti                  |

| Honduras               |

| Iceland                |

| Iraq                   |

| Israel                 |

| Kuwait                 |

| New Zealand            |

| Panama                 |

| Serbia and Montenegro  |

| Togo                   |

| Trinidad and Tobago    |

| United Arab Emirates   |

| Zaire                  |
### 4. Which stadium has hosted the most World Cup matches?
#### Query
```cypher
MATCH (stadium:Stadium)<-[:PLAYED_IN_STADIUM]-(match:Match)
RETURN stadium.name AS StadiumName, COUNT(match) AS MatchCount
ORDER BY MatchCount DESC
LIMIT 1
```
#### Output

| StadiumName    | MatchCount |
| -------------- | ---------- |
| Estadio Azteca | 19         |


### 5. Which country has participated in the most World Cups?
#### Query 
```cypher
MATCH (country:Country)-[:NAMED_SQUAD]->(squad:Squad)-[:FOR_WORLD_CUP]->(worldCup:WorldCup)
RETURN country.name AS CountryName, COUNT(DISTINCT worldCup) AS WorldCupCount
ORDER BY WorldCupCount DESC
LIMIT 1

```
| CountryName | WorldCupCount |
| ----------- | ------------- |
|         Brazil    |     21          |

### 6. Which hosts won the World Cup that they hosted?
#### Query
```cypher

MATCH (wc:WorldCup)-[:HOSTED_BY]->(host:Country)


MATCH (wc)-[:CONTAINS_MATCH]->(final:Match {round: "Final"})


MATCH (final)-[:HOME_TEAM]->(home:Country)
MATCH (final)-[:AWAY_TEAM]->(away:Country)
WHERE (home = host AND final.h_score > final.a_score) OR
      (away = host AND final.a_score > final.h_score)


RETURN wc.year AS WorldCupYear, 
       host.name AS HostCountry, 
       final.h_score AS HomeScore, 
       final.a_score AS AwayScore
ORDER BY wc.year

```
#### Output
| WorldCupYear | HostCountry | HomeScore | AwayScore |
|--------------|-------------|-----------|-----------|
| 1930         | Uruguay     | 4         | 2         |
| 1934         | Italy       | 2         | 1         |
| 1966         | England     | 4         | 2         |
| 1974         | Germany FR  | 1         | 2         |
| 1978         | Argentina   | 3         | 1         |
| 1998         | France      | 0         | 3         |
### 7. What's the highest number of goals scored in a World Cup match?
#### Query
```cypher 
MATCH (m:Match)
WITH m, (m.h_score + m.a_score) AS total_goals

ORDER BY total_goals DESC
RETURN m.description AS MatchDescription, 
       total_goals AS TotalGoals
```
#### Output
| MatchDescription          | TotalGoals |
| ------------------------- | ---------- |
| "Austria vs. Switzerland" | 12         |
### 8. Top scorer playing in the 2018 World Cup?
#### Query
```cypher
MATCH (wc:WorldCup {year: 2018})-[:CONTAINS_MATCH]->(m:Match)
MATCH (a:Appearance)-[:IN_MATCH]->(m)
MATCH (a)-[:SCORED_GOAL]->(g:Goal)
MATCH (p:Player)-[:STARTED|:SUBSTITUTE]->(a)

WITH p, count(g) AS total_goals
ORDER BY total_goals DESC
LIMIT 1

RETURN p.name AS PlayerName, 
       total_goals AS TotalGoals
```
#### Output
| PlayerName   | TotalGoals |
| ------------ | ---------- |
| "Harry Kane" | 6          |
### 9. Who hosted the World Cup more than once, and when?
#### Query 
```cypher
MATCH (wc:WorldCup)-[:HOSTED_BY]->(host:Country)
WITH host.name AS HostCountry, collect(wc.year) AS YearsHosted
WHERE size(YearsHosted) > 1

RETURN HostCountry, YearsHosted
ORDER BY HostCountry
```
#### Output
| HostCountry | YearsHosted  |
| ----------- | ------------ |
| "Brazil"    | [2014, 1950] |
| "France"    | [1998, 1938] |
| "Italy"     | [1990, 1934] |
| "Mexico"    | [1986, 1970] |
### 10. Top scorer playing in the 2018 World Cup?
Answered before [Go to Question 8](#8-top-scorer-playing-in-the-2018-world-cup)
