---
layout: page
title:  "O projekcie"
date:   2020-06-04 20:07:49 +0200
---

# O projekcie, motywacja

Celem projektu jest analiza ruchu miejskich autobusów w Krakowie i ich ewentualnych spóźnień. Na podstawie wyników planujemy stworzyc pełnoprawną aplikację webową słuzącą do przewidywania opóźnień konkretnych autobusów na danej linii w danym momencie. 

Kogo nie nurtują spóźnienia? Naszą motywacją jest uzyskanie odpowiedzi, czy i ile minut (w Krakowie autobusy zawsze się spóźniają) mój autobus będzie dziś spóźniony. Na spóźnienie ma na pewno wpływ bardzo wiele czynników, które postaramy się przeanalizowac, ale najpierw od początku.

Od kilku lat MPK Kraków udostępnia dane między innymi z tablic elektronicznych znajdujących się na przystankach (ttss.krakow.pl), co za tym idzie mamy udostępnione API podające nam informacje o lokalizacji, trasie i planowanym odjeździe poszczególnych autobusów i tramwajów. 

Dane są pozyskiwane z czujników GPS znajdujących się w pojazdach, wysyłane są do serwerowni, do której mozemy juz wykonywac zapytania HTTP o dany pojazd, lub określoną linię autobusową. API udostępnia dosyc fajną funkcjonalnośc, której zaraz się przyjrzymy - otóz mozemy zaobserowac, w której minucie pojazd dojechał (bądź opuścił) przystanek - co za tym idzie w łatwy sposób mozemy zdefiniowac spóźnienie.

## Dostępne API

Na wstępie musimy wyróźnic kilka podstawowych elementów API, z których będziemy korzystac:

* `tripId` - identyfikator konkretnego przejazdu na określonej trasie,
* `vehicleId` - identyfikator pojazdu,
* `routeId` - identyfikator trasy, w której zawierają się przejazdy,
* `stopId` - identyfikator przystanku.

Teraz mozemy przejśc do omówienia kolejnych endpointów:

* <a href="http://91.223.13.70/internetservice/geoserviceDispatcher/services/vehicleinfo/vehicles">`/vehicles`</a>  - zwraca listę wszystkich rejestrowanych pojazdów, wraz z `tripId`,
* <a href="http://91.223.13.70/internetservice/services/routeInfo/routeStops?routeId=8095257447305839175">`/routeInfo/routeStops?routeId=routeId`</a> - zwraca numery i nazwy przystanków na trasie o podanym ID, w kolejności alfabetycznej,
* <a href="http://91.223.13.70/internetservice/geoserviceDispatcher/services/stopinfo/stops?left=-648000000&bottom=-324000000&right=648000000&top=324000000">`/stops`</a> - zwraca listę przystanków w Krakowie, 
* <a href="http://91.223.13.70/internetservice/services/passageInfo/stopPassages/stop?stop=77">`/?stop=stopId`</a> - zwraca informację o konkretnym przystanku, wraz z informacjami o `routeId` i `tripid`,
* <a href="http://91.223.13.70/internetservice/services/tripInfo/tripPassages?tripId=8095261304188834572&vehicleId=-1152921504094991939">`/tripPassages?tripId=tId&vehicleId=vId`</a> - zwraca trasę i przewidywane czasy przyjazdu tramwaju o podanym vID, realizującego przejazd tID.

Odpytując kolejne endpointy jesteśmy w stanie uzyskac informacje o odjazdach z wybranego przez nas przystanku. Najbardziej przydatny będzie nam endpoint `/?stop=stopId`, poniewaz zawiera ogół potrzebnych informacji do zdefiniowania opóźnienia. 

Dane będą musiały byc zbierane przez kilka dni, zeby jak najlepiej uogólnic opóźnienia w skali całego tygodnia. 


## Milestone'y

1. Pierwszym etapem będzie integracja bazy danych z dostępnym API oraz zdobycie pierwszych informacji o konkretnej lini autobusowej.
2. Jako drugi etap postaramy się zebrac dane dla całego Krakowa i w sensowny sposób je opisac i zaprezentowac.
3. Trzecim etapem będzie dokładna analiza, z wyszczególnieniem konkretnej lini, jak i ogólną predykcją dla całego miasta.
4. Prezentacja pierwszych rezultatów, ich analiza i ocena przydatności.
5. Budowa modeli predykcyjnych i statystycznych.
6. Implementacja uzytecznej aplikacji webowej.


## Jakie dane chcemy zbierać?
Na pewno będą to dane w pełni reprezentujące dany przejazd - `tripId` oraz `vehicleId` - następnie musimy zebrać kolejne numery przeystanków (na których będziemy mierzyć opóźnienia), co za tym idzie przydadzą się jeszcze informacje o przystankach (identyfikator), data i godzina pomiaru, wartość opóźnienia, oraz jeśli się uda to natężenie ruchu w okolicy przystanku o danej godzinie - tutaj na pomoc przyjdzie nam Google Maps API.


