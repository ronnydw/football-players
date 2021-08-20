# football-players

Use the Kaggle dataset on record transfers for football-players (scraped from the wiki page)

## Load into a neo4j graph

```
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/ronnydw/football-players/main/football-players.csv" AS row
FIELDTERMINATOR ';'
WITH row
MERGE (p:Player {name: row.Player})
    ON CREATE SET p.born = row.Born, p.position = row.Position
MERGE (fcl:Club {name: row.FromClub})
MERGE (tcl:Club {name: row.ToClub})
MERGE (fcy:Country {name: row.FromCountry})
MERGE (tcy:Country {name: row.ToCountry})
MERGE (ocy:Country {name: row.Origin})
MERGE (p)-[:ORIGIN]->(ocy)
MERGE (fcl)-[:IN_COUNTRY]->(fcy)
MERGE (tcl)-[:IN_COUNTRY]->(tcy)
MERGE (t:Transfer {rank: row.Rank})
    ON CREATE SET t.fee = row.FeeMEur, t.year = row.Year
MERGE (p)-[:TRANSFERRED]->(t)
MERGE (t)<-[:TRANSFER_FROM]-(fcl)
MERGE (t)-[:TRANSFER_TO]->(tcl)
```

## Queries

### Show transfers of "Romelu Lukaku"
```
MATCH p=(:Country)-[]-(pl:Player)-[:TRANSFERRED]->(:Transfer)-[]-(:Club)-[]->(:Country)
WHERE pl.name = 'Romelu Lukaku'
RETURN p
```
