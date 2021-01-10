# Exploration at het.io
1. Collect Symptoms for each of the disease
```
MATCH (d:Disease)--(s:Symptom) RETURN d.name,collect(s.name)
```
2. Know schema of the graph data
```
CALL db.schema()
```

<img src="https://github.com/pyncx/neo4j_practice/raw/main/graph.png" width="300" height="300"></img>

3. Collect genes associated with each of the disease
```
MATCH (d:Disease)--(g:Gene) RETURN d.name,collect(g.name)
```
4. Count gene and pathways for each diseases
```
MATCH (d:Disease)--(g:Gene)--(p:Pathway) RETURN d.name,count(g.name),count(p.name)
```
5. Count and collect associated compound with disease
```
MATCH (d:Disease)--(c:Compound)--(s:SideEffect) RETURN d.name,count(DISTINCT c.name),collect(DISTINCT c.name)
```

---------------------------------------

# Exploration at reactome.org

1. What are the diseases included in Reactome database?
```
MATCH (d:Disease) RETURN d.name, d.definition, d.dbId, d.synonym
```
2. What about Pathways?
```
MATCH (p:Pathway) RETURN p.displayName, p.schemaClass, p.stId, p.speciesName
```
3. Homany drugs are there?
```
MATCH (d:Drug) RETURN d.displayName, d.name, d.stId, d.dbId
```
4. Collect pathways for each disease
```
MATCH (d:Disease)--(p:Pathway) RETURN d.name,count(p.name),collect(p.name)
```
5. Collect reactions for each pathways

```
MATCH (pw:Pathway)-[:hasEvent]->(rx:Reaction) RETURN pw.name,count(rx.name),collect(rx.name)
```

--------------------------------------

# Exploration at covidgraph.org

1. What is there in the paper?
```
MATCH (n:Paper) RETURN n LIMIT 4
```
2. Find papers mentioning about proteins
```
MATCH (a:FromBodyText)-[r:MENTIONS]->() RETURN a LIMIT 5
```
3. Collect fragments for gene symbol
```
MATCH (p:Paper)-[:PAPER_HAS_BODYTEXTCOLLECTION]-(:BodyTextCollection)\
        -[:BODYTEXTCOLLECTION_HAS_BODYTEXT]-(:BodyText)-[:HAS_FRAGMENT]\
        -(f:Fragment)-[:MENTIONS]->(g:GeneSymbol)\
        RETURN f.text limit 100
```
4. Collect disease fragment for pathways involvment
```
MATCH (p:Paper)-[:PAPER_HAS_BODYTEXTCOLLECTION]-(:BodyTextCollection)\
        -[:BODYTEXTCOLLECTION_HAS_BODYTEXT]-(:BodyText)-[:HAS_FRAGMENT]\
        -(f:Fragment)-[:MENTIONS]->(g:GeneSymbol)\
        WITH f,g\
        MATCH ((g:GeneSymbol)<--(gn:Gene)-->(pw:Pathway))\
        WITH f,g,gn,pw\
        MATCH ((g:GeneSymbol)<--(gn:Gene)<--(d:Disease))\
        WHERE d.name='lung cancer'\
        RETURN f.text,gn.Symbol_from_nomenclature_authority,pw.name, d.name limit 100
 
 
 ```
