# dynamoDB_RAP_Albuns

<h1>COMANDOS UTILIZADOS NO PROJETO</h1>

<h2>CRIAÇÃO DA TABELA MÚSICAS</h2>

```
aws dynamodb create-table \
    --table-name Musicas \
    --attribute-definitions \
        AttributeName=Artista,AttributeType=S \
        AttributeName=Titulo,AttributeType=S \
    --key-schema \
        AttributeName=Artista,KeyType=HASH \
        AttributeName=Titulo,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

<h2>INSERÇÃO DAS MÚSICAS POR ALBÚM</h2>

```
aws dynamodb batch-write-item \
    --request-items file://heresia.json
```
```
aws dynamodb batch-write-item \
    --request-items file://qvvjfa.json
```
```
aws dynamodb batch-write-item \
    --request-items file://icarus.json
```

<h2>CRIAÇÃO DOS INDEXES GLOBAIS SECUNDÁRIOS</h2>

<h3>TÍTULO DO ALBUM</h3>

```
aws dynamodb update-table \
    --table-name Musicas \
    --attribute-definitions AttributeName=Titulo,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"Titulo-index\",\"KeySchema\":[{\"AttributeName\":\"Titulo\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```
<h3>ARTISTA E TÍTULO</h3>

```
aws dynamodb update-table \
    --table-name Musicas \
    --attribute-definitions\
        AttributeName=Artista,AttributeType=S \
        AttributeName=Titulo,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"ArtistaTitulo-index\",\"KeySchema\":[{\"AttributeName\":\"Artista\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"Titulo\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```
<h3>ALBUM E ANO DO ALBUM</h3>

```
aws dynamodb update-table \
    --table-name Musicas \
    --attribute-definitions\
        AttributeName=Album,AttributeType=S \
        AttributeName=AnoLancamento,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"AlbumAno-index\",\"KeySchema\":[{\"AttributeName\":\"Album\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"AnoLancamento\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

<h2>TESTE COM QUERIES</h2>

<h3>MÚSICAS POR ARTISTA</h3>

```
aws dynamodb query \
    --table-name Musicas \
    --key-condition-expression "Artista = :artista" \
    --expression-attribute-values  '{":artista":{"S":"Djonga"}}'
```
```
aws dynamodb query \
    --table-name Musicas \
    --key-condition-expression "Artista = :artista" \
    --expression-attribute-values  '{":artista":{"S":"Baco Exu do Blues"}}'
```

<h3>ARTISTA E TÍTULO DA MÚSICA</h3>

```
aws dynamodb query \
    --table-name Musicas \
    --index-name ArtistaTitulo-index \
    --key-condition-expression "Artista = :artista and Titulo = :titulo" \
    --expression-attribute-values  '{":artista":{"S":"Djonga"},":titulo":{"S":"Geminiano"}}'
```
<h3>ALBUM E ANO DE LANCAMENTO</h3>

```
aws dynamodb query \
    --table-name Musicas \
    --index-name AlbumAno-index \
    --key-condition-expression "Album = :album and AnoLancamento = :ano" \
    --expression-attribute-values  '{":album":{"S":"Icarus"},":ano":{"S":"2022"}}'
```

```
aws dynamodb query \
    --table-name Musicas \
    --index-name AlbumAno-index \
    --key-condition-expression "Album = :album and AnoLancamento = :ano" \
    --expression-attribute-values  '{":album":{"S":"Heresia"},":ano":{"S":"2017"}}'
```




