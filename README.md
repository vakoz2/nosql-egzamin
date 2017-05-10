# Projekt Aggregation Pipeline (egzamin)

### Michał Kiełb, Łukasz Szlas, Tomasz Smogór

## Dane

[Football Events](https://www.kaggle.com/secareanualin/football-events).

Kolekcja *events*:

Ilość dokumentów: **941,009**

Przykładowy dokument:

```json
{
	"_id" : ObjectId("591208756069f688d1d0f3c3"),
	"id_odsp" : "UFot0hit/",
	"id_event" : "UFot0hit1",
	"sort_order" : 1,
	"time" : 2,
	"text" : "Attempt missed. Mladen Petric (Hamburg) left footed shot from the left side of the box is high and wide to the left. Assisted by Gokhan Tore.",
	"event_type" : 1,
	"event_type2" : 12,
	"side" : 2,
	"event_team" : "Hamburg SV",
	"opponent" : "Borussia Dortmund",
	"player" : "mladen petric",
	"player2" : "gokhan tore",
	"player_in" : "NA",
	"player_out" : "NA",
	"shot_place" : 6,
	"shot_outcome" : 2,
	"is_goal" : 0,
	"location" : 9,
	"bodypart" : 2,
	"assist_method" : 1,
	"situation" : 1,
	"fast_break" : 0
}
```

Kolekcja *ginf*:

Ilość dokumentów: **10,112**

Przykładowy dokument:

```json
{
	"_id" : ObjectId("591208d36069f688d1df5348"),
	"id_odsp" : "UFot0hit/",
	"link_odsp" : "/soccer/germany/bundesliga-2011-2012/dortmund-hamburger-UFot0hit/",
	"adv_stats" : "TRUE",
	"date" : "2011-08-05",
	"league" : "D1",
	"season" : 2012,
	"country" : "germany",
	"ht" : "Borussia Dortmund",
	"at" : "Hamburg SV",
	"fthg" : 3,
	"ftag" : 1,
	"odd_h" : 1.56,
	"odd_d" : 4.41,
	"odd_a" : 7.42,
	"odd_over" : "NA",
	"odd_under" : "NA",
	"odd_bts" : "NA",
	"odd_bts_n" : "NA"
}
```

## Agregacje

1. Przedziały czasowe, w których trafiano bramki.
2. Celne strzały względem pozycji z której oddano strzał, a miejsca w które trafiono.
3. Wpływ własnego boiska na wygraną.
4. Bilans Barcelony w 'El Clasico'.
5. Statystyki goli Lionela Messiego.
6. Zmiana trenera w zespole Manchester United a liczba zwycięstw.

### Prezentacja

[Link do prezentacji](https://github.com/mkielb/nosql-egzamin/blob/master/presentation.pdf)

#### Zapytania do mongo z użyciem Pythona.
Aby móc korzystać w Pythonie z dostępu do do bazy w mongo należy zainstalować pakiet <b>PyMongo</b>. Następnie korzystamy ze szkieletu:
```
import pymongo
client = pymongo.MongoClient()
db = client.test
```
gdzie <i>test</i> to nazwa bazy. Następnie kopiujemy nasze zapytanie dodając apostrofy do stringów. Dla pierwszej agregacji wygląda to tak:
```
aggr = db.events.aggregate( [
	{
	'$facet': {
		'categorizedByGoal': [
			{ '$match': { 'is_goal': { '$eq': 1 } } },
			{
				'$bucket': {
					'groupBy': '$time',
      					'boundaries': [ 0, 46, 91, 120 ],
      					'default': 'Other',
      					'output': {
        					'count': { '$sum': 1 },
      					}
    				}
  			}
		],
	}
	}
]
)
for x in aggr:
	print (x)
```
Porównanie otrzymanych wyników:
mongo:
```

```
python:
```
C:\Users\vakoz\nosql>python script.py
{'categorizedByGoal': [{'_id': 0, 'count': 10744}, {'_id': 46, 'count': 13433}, {'_id': 91, 'count': 269}]}
```
